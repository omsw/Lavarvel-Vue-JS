<template>
        <div class="row mt-5">
          <div class="col-md-12">
            <div class="card">
              <div class="card-header">
                <h3 class="card-title">Users List</h3>

                <div class="card-tools">


                <button class="btn btn-success"  data-toggle="modal" @click="addModel()"><i class="fas fa-user-plus"></i> Add New</button>

                </div>
              </div>
              <!-- /.card-header -->
              <div class="card-body table-responsive p-0">
                <table class="table table-hover">
                  <thead>
                    <tr>
                      <th>ID</th>
                      <th>Name</th>
                      <th>Email</th>
                      <th>Type</th>
                      <th>Created At</th>
                      <th>Action</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="user in users" :key="user.id">
                      <td v-text="user.id"></td>
                      <td v-text="user.name"></td>
                      <td v-text="user.email"></td>
                      <td >{{user.type | upText}}</td>
                      <td > {{user.created_at | myDate}}</td>
                      <td>
                        <a class="btn btn-warning" @click="editModel(user)"><i class="fas fa-pencil-alt"></i></a>
                         <a class="btn btn-danger" @click="deleteUser(user.id)"><i class="fas fa-trash"></i></a>
                      </td>
                    </tr>

                  </tbody>
                </table>
              </div>
              <!-- /.card-body -->
            </div>
            <!-- /.card -->
          </div>

              <!-- Modal -->

         <div class="modal fade show" id="modal-default">
                    <div class="modal-dialog">
                    <div class="modal-content">
                        <div class="modal-header">
                        <h4 class="modal-title">{{modelHeading}}</h4>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">×</span>
                        </button>
                        </div>


                         <form @submit.prevent="editMode ? updateUser() :createUser()" @keydown="form.onKeydown($event)">

                        <div class="modal-body">

                            <div class="form-group">
                            <input v-model="form.name" type="text" name="name" placeholder="Name"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                            <has-error :form="form" field="name"></has-error>
                            </div>

                            <div class="form-group">
                            <input v-model="form.email" type="email" name="email" placeholder="Email"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                            <has-error :form="form" field="email"></has-error>
                            </div>

                            <div class="form-group">
                            <input v-model="form.password" type="password" name="password" placeholder="Password"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                            <has-error :form="form" field="password"></has-error>
                            </div>

                            <div class="form-group">
                            <select v-model="form.type"  name="type"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                                <option value="">Select Type</option>
                                 <option value="user">User</option>
                                  <option value="admin">Admin</option>
                                  <option value="author">Author</option>
                            </select>
                            <has-error :form="form" field="type"></has-error>
                            </div>

                             <div class="form-group">
                            <input v-model="form.bio" type="text" name="bio" placeholder="Short Bio"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }">
                            <has-error :form="form" field="bio"></has-error>
                            </div>


                        </div>
                        <div class="modal-footer">
                        <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
                        <button :disabled="form.busy" v-show="!editMode" type="submit" class="btn btn-primary" >Create</button>
                        <button :disabled="form.busy" v-show="editMode" type="submit" class="btn btn-warning" >Update</button>
                        </div>
                     </form>

                    </div>
                    <!-- /.modal-content -->
                    </div>
                    <!-- /.modal-dialog -->
        </div>


        <!-- Modal -->
        </div>


</template>

<script>
    export default {
        data(){
            return {
                editMode: false,
                modelHeading:'',
                users : {},
                form : new Form({
                    id : '',
                    name: '',
                    email: '',
                    password:'',
                    type:'',
                    bio:'',
                    photo:''

                })
            }

        },
        methods:{
            addModel(){
               this.modelHeading = "Add New";
               this.form.reset();
                $("#modal-default").modal("show");
            },
            editModel(user){
                this.modelHeading = "Edit User";
                this.editMode = true;
                this.form.reset();
                $("#modal-default").modal("show");
                this.form.fill(user);
            },
            loadUsers(){

                axios.get('api/user').then(({data}) =>( this.users = data.data));

            },
            createUser(){

                this.editMode = false;
                this.$Progress.start();
                this.form.post('api/user')
                .then(()=>{

                    this.loadUsers();
                    this.form.reset();
                    this.$Progress.finish();
                    $("#modal-default").modal("hide");
                    toast.fire({
                    type: 'success',
                    title: 'User Created successfully'
                    });

                })
                .catch(()=>{

                    this.$Progress.finish();
                    toast.fire({
                    type: 'error',
                    title: 'Somthing went wrong!'
                    });

                })


            },
            updateUser(){

                this.$Progress.start();
                    this.form.put('/api/user/'+this.form.id).then(()=>{
                               toast.fire({
                                type: 'success',
                                title: 'User updated successfully'
                                });
                                $("#modal-default").modal("hide");
                               this.loadUsers();

                        }).catch(()=>{

                           this.$Progress.fail();
                           toast.fire({
                            type: 'error',
                            title: 'Somthing went wrong!'
                            });


                      });

                    this.$Progress.finish();



            },
            deleteUser(id){

                Swal.fire({
                title: 'Are you sure?',
                text: "You won't be able to revert this!",
                type: 'warning',
                showCancelButton: true,
                confirmButtonColor: '#3085d6',
                cancelButtonColor: '#d33',
                confirmButtonText: 'Yes, delete it!'
                }).then((result) => {

                       if (result.value) {

                        this.form.delete('/api/user/'+id).then(()=>{
                                Swal.fire(
                                'Deleted!',
                                'User has been deleted.',
                                'success'
                                )
                               this.loadUsers();


                        }).catch(()=>{

                            Swal.fire(
                                'Failed!',
                                'Something went worng!',
                                'error'
                                )


                      });

                }



                });


            }



        },
        mounted() {
            this.loadUsers();
        }
    }
</script>
