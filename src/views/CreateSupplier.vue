<template>
    <div>
            <v-sheet width="300" class="mx-auto">
              <v-form @submit.prevent>
                <v-text-field
                  v-model="namaSupplier"
                  :rules="rules"
                  label="Nama Supplier"
                  required
                ></v-text-field>
                <v-text-field
                  v-model="alamatSupplier"
                  :rules="rules"
                  label="Alamat"
                  required
                ></v-text-field>
                <v-text-field
                  v-model="noTelp"
                  :rules="rules"
                  label="Stok"
                  required
                ></v-text-field>
                <v-btn type="v-btn" @click="adddata" block class="mt-2">Submit</v-btn>
                <v-btn type="v-btn" @click="gotohome" block class="mt-2">Cancel</v-btn>
              </v-form>
            </v-sheet>
    </div>
</template>

<script>
    import router from '@/router'
    import Axios from 'axios'
    
export default {
    name : 'Createbarang',
    data() {
        return {
            namaSupplier : '',
            alamatSupplier : '',
            noTelp : '',
            rules : [v => !!v || 'Item is required'],
            token: localStorage.getItem('token') || null
        }
    },

    methods: {
        adddata() {
            Axios.post('http://159.223.57.121:8090/supplier/create', 
              {
                  "alamat": this.alamatSupplier,
                  "namaSupplier": this.namaSupplier,
                  "noTelp": this.noTelp
            }, {
            headers: {
              'accept': '*/*',
              'Content-Type': 'application/json',
              'Authorization': 'Bearer ' + this.token,
              'Access-Control-Allow-Origin': '*'
            }})
            .then((resp) => {
              if (resp.status === 200 ){
                console.log("berhasil create supplier")
                this.gotohome()
              } else {
                  this.msg = resp.data.message
                  this.gagalmodal = true
              }
            })
            .catch(err => console.error(err)) 
        },
        gotohome() {
          // router.push('/homepage')
          router.go(-1)
        }
    },
}
</script>

<style lang="">
    
</style>