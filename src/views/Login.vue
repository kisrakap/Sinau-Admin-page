<template>
  <div class="home pa-10 ma-3">
    <v-row >
      <v-col cols="6">
        <img alt="Vue logo" src="../assets/logo.png">
      </v-col>
      <v-col cols="6">
        <v-container class="ma-3 pa-3" >
          <v-card outlined elevation="20" class="rounded-xl">
            <v-form ref="form" class="ma-1 pa-2 rounded outlined" elevation="20">
              <v-text-field
                v-model="username"
                label="Name" 
                class="rounded-xl"
                outlined
                :rules="[v => !!v || 'Name is required']"
                required
              ></v-text-field>
          
              <v-text-field
                v-model="password"
                label="password"
                type="password"
                class="rounded-xl"
                outlined
                :rules="[v => !!v || 'Password is required']"
                required
              ></v-text-field>
              
              <v-hover v-slot="{ hover }">
                <v-btn
                  class="me-4"
                  @click="login"
                  color="primary"
                >
                  submit
                </v-btn>
              </v-hover>
                <router-link to="/register" class="row-pointer">
                  <span> Belum punya akun ? register disini </span>
                </router-link>
   
            </v-form>
          </v-card>
        </v-container>
      </v-col>
    </v-row>
    <v-dialog
      v-model="gagalmodal"
      width="auto"
    >
      <v-card>
        <v-card-text>
          {{"gagal login"}} {{ msg }}
        </v-card-text>
        <v-card-actions>
          <v-btn color="primary" block @click="gagalmodal = false">Close Dialog</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
// @ is an alias to /src
import router from '@/router'
import Axios from 'axios'
export default {
  name: 'Loginpage',
  components: {
  },
  data() {
    return {
      username : '',
      password : '',
      gagalmodal : false,
      msg : ''
    }
  },
  methods : {
    async login () {
      if (localStorage.getItem('token')) {
        router.push('/homepage')
      }
      const { valid } = await this.$refs.form.validate()
      if (valid) {
        Axios.post('http://159.223.57.121:8090/auth/login', 
       {
         username : this.username, password : this.password
       }, {
       headers: {
         'accept': '*/*',
         'Content-Type': 'application/json'
       }})
       .then((resp) => {
         if (resp.status === 200 && resp.data.data.id){
           this.token = resp.data?.data?.token || "" 
           localStorage.setItem('token', this.token)
           router.push('/homepage')
         } else if (resp.message ){
           this.msg = resp.message
           this.gagalmodal = true
         } else {
           this.msg = resp.data.message
          this.gagalmodal = true
         }
       })
       .catch((err) => {
        console.error(err)
         this.gagalmodal = true
      }) 
      }

    },
    gotoregister(){
      router.push('/register')
    }

  }

  
}
</script>
<style>
.row-pointer {
  cursor: pointer;
}
</style>
