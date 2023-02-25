<template>
  <v-container>
    <v-row>
      <v-col>
        <div class="rounded-lg border" elevation="20">
                <v-sheet width="300" class="mx-auto">
                  <v-form @submit.prevent>
                    <v-text-field
                      v-model="namaBarang"
                      :rules="rules"
                      label="Nama Barang"
                      required
                    ></v-text-field>
                    <v-text-field
                      v-model="hargaBarang"
                      :rules="rules"
                      label="Harga Barang"
                      type="number"
                      required
                    ></v-text-field>
                    <v-text-field
                      v-model="stokBarang"
                      :rules="rules"
                      label="Stok"
                      required
                    ></v-text-field>
                    <v-select
                        v-model="namaSupplier"
                        :items="items"
                        :rules="[v => !!v || 'Item is required']"
                        item-title="namaSupplier"
                        required
                        return-object
                    ></v-select>
                    <v-btn type="v-btn" @click="adddata" block class="mt-2">Submit</v-btn>
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
            namaBarang : '',
            hargaBarang : 0,
            stokBarang : 0,
            namaSupplier : {},
            supplier : {},
            items : [],
            rules : [v => !!v || 'Item is required'],
            token: localStorage.getItem('token') || null

        }
    },
    mounted() {
        this.getSupplier()
    },
    watch:{
        namaSupplier(){
        this.supplier = this.namaSupplier

      }
    },
    methods: {
        updatedata() {
            router.push()
        },
        adddata() {
            Axios.post('http://159.223.57.121:8090/barang/create', 
            {
                "harga": this.hargaBarang,
                "namaBarang": this.namaBarang,
                "stok": this.stokBarang,
                "supplier": this.supplier
            }, {
            headers: {
              'accept': '*/*',
              'Content-Type': 'application/json',
              'Authorization': 'Bearer ' + this.token,
              'Access-Control-Allow-Origin': '*'
            }})
            .then((resp) => {
              if (resp.status === 200 ){
                console.log("berhasil")
                this.gotohome()
              } else {
                  this.msg = resp.data.message
                  this.gagalmodal = true
              }
            })
            .catch(err => console.error(err)) 
        },
        getSupplier() {
             Axios.get(`http://159.223.57.121:8090/supplier/find-all?limit=100&offset=1`, 
              {
                headers: {
                  'accept': '*/*',
                  'Content-Type': 'application/json',
                  'Authorization': 'Bearer ' + this.token // token adalah nilai dari authorization token yang diperoleh sebelumnya
              }
            })
            .then((resp) => {
              if (resp.status === 200){
                  this.items= resp.data.data
              } else {
                  this.msg = resp.data.message
                  this.gagalmodal = true
              }
            })
            .catch(err => console.error(err)) 

    },
    },
}
</script>
<style lang="">
    
</style>