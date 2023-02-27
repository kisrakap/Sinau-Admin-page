<template>
    <div>
      <v-container>
        <h3> Update Data</h3>
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
                        <v-btn variant="tonal" type="v-btn" @click="updatedata" block class="mt-2">Submit</v-btn>
                        <v-btn type="v-btn" @click="gotohome" color="warning" block class="mt-2">Cancel</v-btn>
                      </v-form>
                    </v-sheet>
            </div>
          </v-col>
        </v-row>
      </v-container>
      </div>
</template>
<script>
    import router from '@/router'
    import Axios from 'axios'
    
export default {
    name : 'EditSupplier',
    components : [],
    data() {
        return {
            namaSupplier : '',
            alamatSupplier : '',
            noTelp : '',
            supplierId : '',
            detail : {},
            rules : [v => !!v || 'Item is required'],
            token: localStorage.getItem('token') || null
        }
    },
    created() {
        this.supplierId = this.$route.params.id;  
        this.getSupplier()
        
    },
    methods: {
      updatedata() {
            Axios.put(`http://159.223.57.121:8090/supplier/update/${this.supplierId}`, 
            {
                "namaSupplier": this.namaSupplier,
                "noTelp": this.noTelp,
                "alamat": this.alamatSupplier,
                "id" : this.supplierId
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
        getSupplier() {
             Axios.get(`http://159.223.57.121:8090/supplier/find-by-id/${this.supplierId}`, 
              {
                headers: {
                  'accept': '*/*',
                  'Content-Type': 'application/json',
                  'Authorization': 'Bearer ' + this.token // token adalah nilai dari authorization token yang diperoleh sebelumnya
              }
            })
            .then((resp) => {
              if (resp.status === 200){
                this.detail = resp.data.data                
                this.namaSupplier = this.detail.namaSupplier,
                this.noTelp = this.detail.noTelp,
                this.alamatSupplier = this.detail.alamat
              } else {
                console.log("test gagal")
              }
            }
            )
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