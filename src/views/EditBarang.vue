<template>
    <div>
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
                  variant="outlined"
                ></v-text-field>
                <div class="text-subtitle-1 text-medium-emphasis">Harga Barang</div>
                <v-text-field
                  v-model="hargaBarang"
                  :rules="rules"
                  type="number"
                  required
                  variant="outlined"
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
                    v-model="namaSupplier"
                    :items="items"
                    :rules="[v => !!v || 'Item is required']"
                    item-title="namaSupplier"
                    required
                    name="namaSupplier"
                    variant="outlined"
                    placeholder="namaSupplier"
                    single-line
                    return-object
                ></v-select>
                <v-btn type="v-btn" @click.prevent="updatedata" block class="mt-2" color="success">Submit</v-btn>
                <v-btn type="v-btn" @click.prevent="gotohome" color="warning" block class="mt-2">Cancel</v-btn>
              </v-form>
            </v-sheet>
          </div>
        </v-col>
      </v-row>
    </div>
</template>
<script>
    import router from '@/router'
    import Axios from 'axios'

export default {
    name : 'Editbarang',
    components : [],
    data() {
        return {
            barangId : '',
            namaBarang : '',
            hargaBarang : 0,
            stokBarang : 0,
            namaSupplier : {'namaSupplier' : ''},
            supplier : {},
            detail : {},
            items : [],
            rules : [v => !!v || 'Item is required'],
            token: localStorage.getItem('token') || null,
            isLoading : false

        }
    },
    created() {
        this.barangId = this.$route.params.id;  
        this.getBarang()
        this.getSuuplier()

    },
    watch:{
        namaSupplier(){
            console.log(this.namaSupplier)
            this.supplier = this.namaSupplier
      }
    },
    methods: {
        getBarang() {
             Axios.get(`http://159.223.57.121:8090/barang/find-by-id/${this.barangId}`, 
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
                this.namaBarang = this.detail.namaBarang
                this.hargaBarang = this.detail.harga
                this.stokBarang = this.detail.stok
                this.namaSupplier = this.detail.supplier
              } else {
                console.log("test")
              }
            }
            )
            .catch(err => console.error(err)) 

    },
        updatedata() {
            Axios.put(`http://159.223.57.121:8090/barang/update/${this.barangId}`, 
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
                router.push('/homepage')
              } else {
                  this.msg = resp.data.message
                  this.gagalmodal = true
              }
            })
            .catch(err => console.error(err)) 
        },
        getSuuplier() {
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
                  this.items= resp.data.data
                  this.isLoading = false
              } else {
              }
            }
            )
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