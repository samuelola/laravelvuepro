<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-12 mt-5">
                
              <div class="card">
                  <div class="card-header">
                    <h3 class="card-title">Users Table</h3>

                    <div class="card-tools">
                      <div class="input-group input-group-sm" style="width: 150px;">
                       
                          <button class="btn btn-success btn-sm wol" @click="newModal">Add New  <i class="fas fa-user-plus fa fw"></i></button> 
               
                       
                      </div>
                    </div>
                  </div>
                  <!-- /.card-header -->
                  <div class="card-body table-responsive p-0">
                     <table class="table">
                       <thead class="thead-dark">
                         <tr>
                           <th scope="col">ID</th>
                           <th scope="col">Name</th>
                           <th scope="col">Email</th>
                           <th scope="col">Role</th>
                           <th scope="col">Bio</th>
                           <th scope="col">Registered_At</th>
                           <th scope="col">Action</th>
                         </tr>
                       </thead>
                       <tbody>
                         <tr v-for="user in users" :key="user.id">
                           
                           <td>{{user.id}}</td>
                           <td>{{user.name}}</td>
                           <td>{{user.email}}</td>
                           <td>{{user.role}}</td>
                           <td>{{user.bio}}</td>
                           <td>{{user.created_at | myDate}}</td>
                           <td>
                             <a href="#" @click = "editModal(user)">
                               <i class="fa fa-edit btn btn-primary btn-xs"></i>
                             </a>
                             <a href="#" @click="deleteUser(user.id)">
                                <i class="fa fa-trash btn btn-danger btn-xs"></i> 
                             </a>
                           </td>
                         </tr>
                        
                       </tbody>
                     </table>
                  </div>
                  <!-- /.card-body -->
                </div>

                <!-- Modal -->
                
                    <div class="modal fade" id="addNew" tabindex="-1" role="dialog" aria-labelledby="addNewLabel" aria-hidden="true">
                      <div class="modal-dialog modal-dialog-centered" role="document">
                        <div class="modal-content">
                          <div class="modal-header">
                            <h5 v-show="editmode" class="modal-title" id="addNewLabel">Edit User</h5>
                            <h5 v-show="!editmode" class="modal-title" id="addNewLabel">Add User</h5>
                            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                              <span aria-hidden="true">&times;</span>
                            </button>
                          </div>
                          <form @submit.prevent="editmode ? updateUser() : createUser()">
                          <div class="modal-body">
                             <div class="form-group">
                                 
                                   <input v-model="form.name" type="text" name="name"
                                   placeholder="Enter your Name" 
                                     class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                                   <has-error :form="form" field="name"></has-error>
                             </div>

                                 <div class="form-group">
                                   
                                   <input v-model="form.email" type="email" name="email"
                                   placeholder="Enter your Email" 
                                     class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                                   <has-error :form="form" field="email"></has-error>
                                 </div>

                                 <div class="form-group">
                                   
                                   <input v-model="form.password" type="password" name="password" id="password" 
                                    placeholder="Enter your Password" 
                                     class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                                   <has-error :form="form" field="password"></has-error>
                                 </div>

                                 <div class="form-group">

                                   <select name="role" id="role" v-model="form.role " class="form-control" :class="{ 'is-invalid': form.errors.has('role') }">
                                       <option value="">Select Role</option>
                                       <option value="admin">Admin</option>
                                       <option value="author">Author</option>
                                        <option value="user">User</option>
                                       <option value="subscriber">Subscriber</option>
                                   </select>
                                   
                                   <has-error :form="form" field="role"></has-error>
                                 </div>



                                 <div class="form-group">
                                   
                                   
                                   <textarea v-model="form.bio" name="bio" id="bio" cols="30" rows="10" class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }" style="margin-top: 0px; margin-bottom: 0px; height: 67px;"  placeholder="Enter your Bio " >
                                       
                                         
                                   </textarea>

                                    <has-error :form="form" field="bio"></has-error>
                                 </div>
                          </div>
                          <div class="modal-footer">
                            <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
                            <button v-show="editmode" type="submit" class="btn btn-success">Update</button>
                            <button v-show="!editmode" type="submit" class="btn btn-primary">Create</button>
                          </div>
                          </form>

                        </div>
                      </div>
                    </div>
                

            </div>
        </div>
    </div>
</template>

<script>
    export default {
        
        data () {

            return {
              editmode:false,  
              users:{},
              // Create a new form instance
              form: new Form({
                id: '',
                name: '',
                email:'',
                password: '',
                role:'',
                bio:'',
                photo:''
                
              })
            }
          },

          methods:{

            updateUser(){
               
               // console.log('updating user');

               this.$Progress.start();
               this.form.put('api/user/'+ this.form.id)
               .then(()=>{

                 Fire.$emit('AfterUpdate');

                 $('#addNew').modal('hide');


                 Swal.fire(
                   'Updated!',
                   'Your file has been updated.',
                   'success'
                 )

                 this.$Progress.finish();
               })
               .catch(()=>{

                   this.$Progress.fail();
               });
            },

            editModal(user){
                  this.editmode = true;
                  this.form.reset();
                  $('#addNew').modal('show');
                  this.form.fill(user);
            },

            newModal(){
                  this.editmode = false;
                  this.form.reset();
                  $('#addNew').modal('show');
            },


            // delete the data 

            deleteUser(id){

            
                Swal.fire({
                  title: 'Are you sure?',
                  text: "You won't be able to revert this!",
                  icon: 'warning',
                  showCancelButton: true,
                  confirmButtonColor: '#3085d6',
                  cancelButtonColor: '#d33',
                  confirmButtonText: 'Yes, delete it!'
                }).then((result) => {

                  if (result.value) {

                    this.form.delete("api/user/" + id).then(()=>{
                       
                       Swal.fire(
                         'Deleted!',
                         'Your file has been deleted.',
                         'success'
                       )

                       Fire.$emit('AfterDelete');

                    })

                    .catch(()=>{

                        Swal.fire(
                           
                           'Failed',
                           'Something is wrong',
                           'warning'

                        )

                    });
                    
                  }


                })

            },
             // get the data with axios
            retrieve(){
                 
                 axios.get("api/user").then(({data})=>(this.users = data.data));
            },

             // submit the form with a POST request
             createUser(){
               
                this.$Progress.start();

                this.form.post('api/user')

                .then(()=>{

                    Fire.$emit('AfterCreate');

                    $('#addNew').modal('hide');
                     
                     Toast.fire({
                       icon: 'success',
                       title: 'User created successfully'
                     })

                this.$Progress.finish(); 
                })
                .catch(()=>{

                   this.$Progress.fail();
               });

                
             }

          },
          

        mounted() {
            // console.log('Component mounted.')
            this.retrieve();
            Fire.$on('AfterCreate',()=>{

                this.retrieve();
            });

            Fire.$on('AfterDelete',()=>{

                this.retrieve();
            });

            Fire.$on('AfterUpdate',()=>{

                this.retrieve();
            });
             //this is to listen to an event
            // setInterval(()=>this.retrieve(), 3000);

        }
    }
</script>
