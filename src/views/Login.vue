<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <v-container>
      <v-form ref="form">
        <v-text-field
          v-model="username"
          label="Name"
          :rules="[v => !!v || 'Name is required']"
          required
        ></v-text-field>
    
        <v-text-field
          v-model="password"
          label="password"
          :rules="[v => !!v || 'Password is required']"
          required
        ></v-text-field>
    
    
        <v-btn
          class="me-4"
          @click="login"
        >
          submit
        </v-btn>
        <v-btn @click="clear">
          clear
        </v-btn>
      </v-form>
    </v-container>
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
           console.log(resp)
           this.token = resp.data?.data?.token || "" 
           localStorage.setItem('token', this.token)
           router.push('homepage')
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
    clear() {

    }
  }

  
}

</script>
