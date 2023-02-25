<template>
    <div>
        <v-sheet width="500" class="mx-auto">
          <v-form @submit.prevent>
            <v-text-field
              v-model="namaSupplier"
              :rules="rules"
              label="Nama Supplier"
              required
              outlined
            ></v-text-field>
            <v-text-field
              v-model="alamatSupplier"
              :rules="rules"
              label="Alamat"
              required
              outlined
            ></v-text-field>
            <v-text-field
              v-model="noTelp"
              :rules="rules"
              label="No Telpon"
              required
              outlined
            ></v-text-field>
            <v-btn variant="tonal" type="v-btn" @click="updatedata" block class="mt-2">Submit</v-btn>
            <v-btn type="v-btn" @click="gotohome" block class="mt-2">Cancel</v-btn>
          </v-form>
        </v-sheet>
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
                "alamat": this.alamat,
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