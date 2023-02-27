<template>
  <v-container>
    <v-row>
      <v-col>
        <div class="rounded-lg border ma-5 pa-5" elevation="20">
                <v-sheet width="500" elevation="20" class="mx-auto pa-5">
                  <v-form @submit.prevent class="ma-5 pa-5 text-left"> 
                    <div class="text-subtitle-1 text-medium-emphasis">Nama Supplier</div>
                    <v-text-field
                      v-model="namaSupplier"
                      :rules="rules"
                      label="Nama Supplier"
                      required
                      class="rounded-lg"
                      variant="outlined"
                    ></v-text-field>
                    <div class="text-subtitle-1 text-medium-emphasis">Alamat Supplier</div>
                    <v-text-field
                      v-model="alamatSupplier"
                      :rules="rules"
                      label="Alamat"
                      required
                      variant="outlined"
                    ></v-text-field>
                    <div class="text-subtitle-1 text-medium-emphasis">Nomor Telpon</div>
                    <v-text-field
                      v-model="noTelp"
                      :rules="rules"
                      label="Nomor Telepon"
                      variant="outlined"
                      required
                    ></v-text-field>
                    <v-btn type="v-btn" @click="adddata" color="success" block class="mt-2">Submit</v-btn>
                    <v-btn type="v-btn" @click="gotohome" color="warning" block class="mt-2">Cancel</v-btn>
                  </v-form>
                </v-sheet>
        </div>
      </v-col>
    </v-row>
  </v-container>
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
                this.gotohome()
              } else {
                  this.msg = resp.data.message
                  this.gagalmodal = true
              }
            })
            .catch(err => console.error(err)) 
        },
        gotohome() {
          router.go(-1)
        }
    },
}
</script>

<style lang="">
    
</style>