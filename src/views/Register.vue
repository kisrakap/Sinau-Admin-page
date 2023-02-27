<template>
  <div class="about">
    <v-container>
      <h3>Register Page</h3>
      <v-row>
        <v-col class="pa-3">
          <v-card class="rounded-xl" outlined elevation="20">
            <form class="rounded-lg pa-3 ma-3" elevation="20">
              <v-text-field
                v-model="username"
                label="Username"
                :rules="[v => !!v || 'Name is required']"
                required
              ></v-text-field>
      
              <v-text-field
                v-model="profileName"
                label="Profile Name"
                :rules="[v => !!v || 'Name is required']"
                required
              ></v-text-field>
          
              <v-text-field
                v-model="password"
                label="password"
                type="password"
                required
                :rules="[v => !!v || 'Name is required']"
              ></v-text-field>
          
          
              <v-btn
                class="me-4"
                @click.prevent="register"
                color="primary"
              >
                submit
              </v-btn>
            
              <router-link to="/" class="row-pointer">
                <span> Sudah mempunyai akun ? Login disini </span>
              </router-link>
            </form>

          </v-card>

        </v-col>
      </v-row>
     
    </v-container>
  </div>
  <v-dialog
  v-model="gagalmodal"
  width="auto"
>
  <v-card>
    <v-card-text>
     {{ msg ? msg : "GAGAL REGISTER" }}
    </v-card-text>
    <v-card-actions>
      <v-btn color="primary" block @click="gagalmodal = false">Close Dialog</v-btn>
    </v-card-actions>
  </v-card>
</v-dialog>
</template>

<script>
// @ is an alias to /src
import router from '@/router'
import Axios from 'axios'
export default {
  name: 'Registerpage',
  components: {
  },
  data() {
    return {
      username : '',
      password : '',
      profileName : '',
      gagalmodal : false,
      msg : ''
    }
  },
  methods : {
    register() {
             Axios.post('http://159.223.57.121:8090/auth/register', 
            {
              username : this.username, password : this.password, profileName : this.profileName
            }, {
            headers: {
              'accept': '*/*',
              'Content-Type': 'application/json'
            }})
            .then((resp) => {
              if (resp.status === 200 && !resp.data.id){
                router.push('/')
              } else {
                  this.msg = resp.data.message
                  this.gagalmodal = true
              }
            })
            .catch(err => console.error(err)) 

    },
    gotologin(){
      router.push('/login')
    }
  }

  
}

</script>
