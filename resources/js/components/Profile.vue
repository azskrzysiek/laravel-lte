<style>
.widget-user-header
{
    background-position: center center;
    background-size: cover;
    height: 250px !important;
}
</style>

<template>
  <div class="container">
    <div class="row">
      <div class="col-md-12 mt-3">
        <div class="card card-widget widget-user">
          <!-- Add the bg color to the header using any of the bg-* classes -->
          <div
            class="widget-user-header text-white"
            style="background-image:url('./img/user-cover.jpg')"
          >
            <h3 class="widget-user-username">{{this.form.name}}</h3>
            <h5 class="widget-user-desc">{{this.form.type}}</h5>
          </div>
          <div class="widget-user-image">
            <img class="img-circle" :src="getProfilePhoto()" alt="User Avatar">
          </div>
          <div class="card-footer">
            <div class="row">
              <div class="col-sm-4 border-right">
                <div class="description-block">
                  <h5 class="description-header">3,200</h5>
                  <span class="description-text">SALES</span>
                </div>
                <!-- /.description-block -->
              </div>
              <!-- /.col -->
              <div class="col-sm-4 border-right">
                <div class="description-block">
                  <h5 class="description-header">13,000</h5>
                  <span class="description-text">FOLLOWERS</span>
                </div>
                <!-- /.description-block -->
              </div>
              <!-- /.col -->
              <div class="col-sm-4">
                <div class="description-block">
                  <h5 class="description-header">35</h5>
                  <span class="description-text">PRODUCTS</span>
                </div>
                <!-- /.description-block -->
              </div>
              <!-- /.col -->
            </div>
            <!-- /.row -->
          </div>
        </div>
      </div>
    </div>
    <div class="row">
    <div class="col-md-12">
            <div class="card">
              <div class="card-header p-2">
                <ul class="nav nav-pills">
                  <li class="nav-item"><a class="nav-link" href="#activity" data-toggle="tab">Activity</a></li>
                  <li class="nav-item"><a class="nav-link active show" href="#settings" data-toggle="tab">Settings</a></li>
                </ul>
              </div><!-- /.card-header -->
              <div class="card-body">
                <div class="tab-content">
                  <div class="tab-pane" id="activity">
                    <!-- Post -->
                    User Activity
                  </div> 

                  <div class="tab-pane active show" id="settings">
                    <form class="form-horizontal">
                      <div class="form-group">
                        <label for="inputName" class="col-sm-2 control-label">Name</label>

                        <div class="col-sm-10">
                          <input type="email" v-model="form.name" class="form-control" id="name" placeholder="Name" :class="{ 'is-invalid': form.errors.has('name') }">
                          <has-error :form="form" field="name"></has-error>
                        </div>
                      </div>
                      <div class="form-group">
                        <label for="inputEmail" class="col-sm-2 control-label">Email</label>

                        <div class="col-sm-10">
                          <input type="email" v-model="form.email" class="form-control" id="email" placeholder="Email" :class="{ 'is-invalid': form.errors.has('email') }">
                          <has-error :form="form" field="email"></has-error>
                        </div>
                      </div>
                      <div class="form-group">
                        <label for="bio" class="col-sm-2 control-label">Experience</label>

                        <div class="col-sm-10">
                          <textarea class="form-control" id="bio" name="bio" v-model="form.bio" placeholder="Experience"></textarea>
                        </div>
                      </div>
                      <div class="form-group">
                        <label for="file" class="col-sm-2 control-label">Photo</label>

                        <div class="col-sm-10">
                          <input type="file" @change="updateProfile" name="photo" class="form-control-file" id="file">
                        </div>
                      </div>
                      <div class="form-group">
                        <label for="password"  class="col-sm-12 control-label">Password (leave empty if not changing</label>

                        <div class="col-sm-12">
                          <input type="password" name="password" v-model="form.password" class="form-control" id="password" placeholder="Password" :class="{ 'is-invalid': form.errors.has('password') }">
                          <has-error :form="form" field="password"></has-error>
                        </div>
                      </div>
                      <div class="form-group">
                        <div class="col-sm-offset-2 col-sm-10">
                          <button @click.prevent="updateInfo" type="submit" class="btn btn-success">Submit</button>
                        </div>
                      </div>
                    </form>
                  </div>
                  <!-- /.tab-pane -->
                </div>
                <!-- /.tab-content -->
              </div><!-- /.card-body -->
            </div>
            <!-- /.nav-tabs-custom -->
          </div>
          </div>
  </div>
</template>

<script>
export default {
  data()
  {
      return {
         form: new Form({
                id: '',
                name: '',
                email: '',
                password: '',
                type: '',
                bio: '',
                photo: ''

            })
      }
  },
  mounted() {
    console.log("Component mounted.");
  },

  methods:{
      getProfilePhoto(){

          let photo = (this.form.photo.length > 100) ? this.form.photo : "img/profile/"+this.form.photo;
          return photo;
      },
      updateInfo(){
          this.$Progress.start();
          if(this.form.password == ""){ this.form.password = undefined; }
          this.form.put('api/profile')
          .then(()=>{
              Fire.$emit('AfterDo'); 

              this.$Progress.finish();
          })
          .catch(()=>{
              this.$Progress.fail();
          });
      },
      updateProfile(e){
        //   console.log("uploading");
        let file = e.target.files[0];
        // console.log(file);
        let reader = new FileReader();
        

        if(file['size'] < 2111775){

            reader.onloadend = (file) => {
                // console.log('RESULT',reader.result)
                this.form.photo = reader.result;
            }
            reader.readAsDataURL(file);
        } else {
             Swal.fire({
                title: 'Oops....',
                text: "You are uploading a large file",
                type: 'error',
             })
        }
      }
  },

  created()
  {
      axios.get("api/profile")
      .then(({data}) => (this.form.fill(data)));

      Fire.$on('AfterDo',()=>{ axios.get("api/profile") .then(({data}) => (this.form.fill(data))); });
  }
};
</script>
