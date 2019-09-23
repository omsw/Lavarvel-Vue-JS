<template>
        <div class="row mt-5">
          <div class="col-md-12">
            <div class="card">
              <div class="card-header">
                <h3 class="card-title">Users List</h3>

                <div class="card-tools">


                <button class="btn btn-success"  data-toggle="modal" data-target="#modal-default"><i class="fas fa-user-plus"></i> Add New</button>

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
                        <a class="btn btn-warning"><i class="fas fa-pencil-alt"></i></a>
                         <a class="btn btn-danger"><i class="fas fa-trash"></i></a>
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
                        <h4 class="modal-title">Add New User</h4>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">×</span>
                        </button>
                        </div>


                         <form @submit.prevent="createUser" @keydown="form.onKeydown($event)">

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
                        <button :disabled="form.busy" type="submit" class="btn btn-primary">Create</button>
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
                users : {},
                form : new Form({
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
            loadUsers(){

                axios.get('api/user').then(({data}) =>( this.users = data.data));

            },
            createUser(){
                this.$Progress.start();
                this.form.post('api/user');
                this.loadUsers();
                this.form.reset();
                this.$Progress.finish();
                $("#modal-default").modal("hide");
                toast.fire({
                type: 'success',
                title: 'User Created in successfully'
                });


            }

        },
        mounted() {
            this.loadUsers();
        }
    }
</script>
