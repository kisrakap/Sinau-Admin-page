<template>
  <v-container>
    <v-row>
      <v-col>
        <div class="rounded-lg border ma-5 pa-5" elevation="20">
                <v-sheet width="500" elevation="20" class="mx-auto pa-5">
                  <v-form @submit.prevent class="ma-5 pa-5 text-left">
                    <div class="text-subtitle-1 text-medium-emphasis">Nama Barang</div>
                    <v-text-field
                      class="rounded-lg"
                      v-model="namaBarang"
                      :rules="rules"
                      required
                      label="Nama Barang"
                      variant="outlined"
                    ></v-text-field>
                    <div class="text-subtitle-1 text-medium-emphasis">Harga Barang</div>
                    <v-text-field
                      v-model="hargaBarang"
                      :rules="rules"
                      type="number"
                      required
                      variant="outlined"
                      label=""
                      min="0"
                    ></v-text-field>
                    <div class="text-subtitle-1 text-medium-emphasis">Stok Barang</div>
                    <v-text-field
                      v-model="stokBarang"
                      :rules="rules"
                      required
                      variant="outlined"
                      min="0"
                    ></v-text-field>
                    <div class="text-subtitle-1 text-medium-emphasis">Pilih Supplier</div>
                    <v-select
                      label="Select"
                      v-model="namaSupplier"
                      :items="items"
                      item-title="namaSupplier"
                      return-object
                      single-line
                      variant="outlined"
                    ></v-select>
                    <v-btn type="v-btn"  variant="elevated" @click.prevent="adddata" block class="mt-2" color="success">Add Data</v-btn>
                    <v-btn type="v-btn" @click.prevent="gotohome" color="warning" block class="mt-2">Cancel</v-btn>
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
            items : [{"a" :"1"}],
            rules : [v => !!v || 'Item is required'],
            token: localStorage.getItem('token') || null,
            isLoading : false

        }
    },
    mounted() {
        this.getSupplier()
    },
    watch:{
        namaSupplier(){
        this.supplier = this.namaSupplier
        console.log(this.namaSupplier)
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
                this.gotohome()
              } else {
                  this.msg = resp.data.message
              }
            })
            .catch(err => console.error(err)) 
        },
        getSupplier() {
            this.isLoading = true
             Axios.get(`http://159.223.57.121:8090/supplier/find-all?limit=50&offset=1`, 
              {
                headers: {
                  'accept': '*/*',
                  'Content-Type': 'application/json',
                  'Authorization': 'Bearer ' + this.token // token adalah nilai dari authorization token yang diperoleh sebelumnya
              }
            })
            .then((resp) => {
              if (resp.status === 200){
                let datas = resp.data.data
                for (let i = 0; i < datas.length; i++) {
                  this.items.push(datas[i]);
                }
                  this.items = datas
                  console.log(this.items)
                  this.isLoading = false
              } else {
                  this.msg = resp.data.message
                  this.isLoading = false
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