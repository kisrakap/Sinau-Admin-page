<template>
  <div class="about pa-5 ma-3">
    <h1>List {{menu}}</h1>
    <v-row>
      <v-col cols="3">
          <v-card
            class="mx-auto my-2"
            max-width="344"
            
            outlined
          >
            <v-img
              src="https://cdn.vuetifyjs.com/images/cards/sunshine.jpg"
              height="200px"
              cover
            ></v-img>
        
            <v-card-title>
              David Smith
            </v-card-title>
        
            <v-card-subtitle>
              ONLINE
            </v-card-subtitle>
          </v-card>
          <v-card
            max-width="344"
            outlined
            :thickness="4"
            class="mx-auto my-2 border-opacity-75"
          >
            <v-card-title>
              Menu
            </v-card-title>
        
            <v-card-subtitle class="bg-dark">
              <v-list-item class="btn bg-dark" fab :disabled="isSupplier" @click.prevent="supplier" :color="isSupplier ? 'blue' : 'primary'">
                Supplier
              </v-list-item>
              <v-list-item class="btn" @click.prevent="barang" :disabled="isBarang" fab :color="isBarang? 'blue' : 'primary'">
                Barang
              </v-list-item>
            </v-card-subtitle>
          </v-card>

          <v-card
            class="mx-auto my-6"
            max-width="344"
            outlined
          >
            <v-card-title @click.prevent="logout">
              Logout
            </v-card-title>
          </v-card>

         
          <!-- <v-btn class="ma-2 " @click.prevent="supplier"> Supplier</v-btn> -->
      </v-col>
      <v-col cols="9">
        <div class="mx-1 pa-2 border rounded-lg" outlined>
          <div class="d-flex justify-space-between mx-2 pa-2">
            <h3>{{menu}}</h3>
            <v-btn class="flex-row-reverse" @click.prevent="addBarang(menu)" :loading="isLoading"> Add {{menu}}</v-btn>
          </div>
          <v-container class="mx-auto pa-2 my-2 border rounded-lg" outlined>
              <v-table v-if="isBarang"
                max-width="2000px"
                height="700px"
                id="tbl"
                :loading="isLoading"
                class="w-100 border-opacity-25"
              >

                <thead>
                  <tr>
                    <th class="text-left">
                      No
                    </th>
                    <th class="text-left">
                      Nama Barang
                    </th>
                    <th class="text-left">
                      Stok
                    </th>
                    <th class="text-left">
                      Harga
                    </th>
                    <th class="text-left">
                      Nama Suplier
                    </th>
                    <th class="text-left">
                      Alamat Suplier
                    </th><th class="text-left">
                      Telepon Suplier
                    </th><th class="text-left">
                       Aksi
                    </th>
                  </tr>
                </thead>
                <tbody class="text-left">
                  <tr
                    v-for="item, index in listbarang"
                    :key="item.id"
                  >
                    <td>{{ (currentPage - 1) * limit + index + 1 }}. {{ item.name }}</td>
                    <td>{{ item.namaBarang }}</td>
                    <td>{{ item.stok }}</td>
                    <td>{{ item.harga }}</td>
                    <td>{{ item.supplier.alamat }}</td>
                    <td>{{ item.supplier.namaSupplier }}</td>
                    <td>{{ item.supplier.noTelp}}</td>
                    <td class="pa-2"> <v-btn @click.prevent="updateData(item)" class="border pa-2 rounded">Update</v-btn> <v-btn class="border pa-2 rounded">Hapus</v-btn></td>
                  </tr>
                </tbody>
               
                <div>
                </div>
              </v-table>
              
              <v-table
              fixed-header
              height="600px" v-if="isSupplier"
              id="tbl"
            >
              <thead>
                <tr>
                  <th class="text-left">
                    No
                  </th>
                  <th class="text-left">
                    Nama Supplier
                  </th>
                  <th class="text-left">
                    Alamat
                  </th>
                  <th class="text-left">
                   No  Telepon
                  </th>
                  <th class="text-left">
                     Aksi
                  </th>
                </tr>
              </thead>
              <tbody class="text-left">
                <tr
                  v-for="item, index in listSupplier"
                  :key="item.id"
                >
                  <td>{{ (currentPage - 1) * limit + index + 1 }}. {{ item.name }}</td>
                  <td>{{ item.namaSupplier }}</td>
                  <td>{{ item.alamat }}</td>
                  <td>{{ item.noTelp}}</td>
                  <td class="pa-2"> <button @click.prevent="updateData(item)" class="border pa-2 rounded">Update</button> <button class="border pa-2 rounded">Hapus</button></td>
                </tr>
              </tbody>
              <div>
              </div>
              </v-table> 
                <v-divider
                :thickness="3"
                class="border-opacity-75"
                color="info"
              ></v-divider>
               <div class="d-flex justify-end text-center align-center">
                <h4 class="mx-2">items in Page </h4> 
                <v-divider
                :thickness="1"
                class="border-opacity-75"
                color="info"
                vertical
              ></v-divider>
                <select class="mx-2 pa-2 border-opacity-50 rounded-lg" id="limit" v-model="limit" @change.prevent="changeLimit">
                  <v-divider
                :thickness="1"
                class="border-opacity-75"
                color="info"
                vertical
              ></v-divider>
                  <option v-for="option in limitOptions" :key="option" :value="option">{{ option }}</option>
                </select>
                 <v-btn  class="mx-2" :disabled="currentPage === 1" @click.prevent="previousPage">Previous</v-btn>
                 <v-btn class="mx-2" :disabled="currentPage === pageCount" @click.prevent="nextPage">Next</v-btn>
                 <h4>
                   Page : {{ currentPage }}
                 </h4>
                </div>
                <div>
                </div>
              
              <div>
              </div>
            </v-container>
            <v-btn class="v-btn btn-primary" @click.prevent="exportExcel">Export to Excel</v-btn>
        </div>


      </v-col>
    </v-row>
    <v-row>
    </v-row>
  </div>
</template>
<script>
import router from '@/router'
import { switchCase } from '@babel/types';
import Axios from 'axios'
// let XLSX = require("xlsx");
import * as XLSX from 'xlsx/xlsx.mjs';

export default {
  name: 'Loginpage',
  components: {
  },
  data() {
    return {
      detaildata : '',
      password : '',
      token: localStorage.getItem('token') || null,
      detail : {},
      listbarang : [],
      currentPage: 1,
      limit: 20,
      limitOptions: [10, 20, 50, 100],
      totalItems: 0,
      totalPage: 0,
      show : false,
      page : '',
      isLoading : false,
      isBarang : true,
      isSupplier : false,
      listSupplier : [],
      menu : 'Barang',
      linkBarang : 'http://159.223.57.121:8090/barang/find-all',
      linkSupplier : 'http://159.223.57.121:8090/supplier/find-all'
    }
  },
  computed: {
    pageCount() {
      return Math.ceil(this.totalItems / this.limit);
    },
    displayedPages() {

      const currentPageGroup = Math.ceil(this.currentPage / 5);
      const startPage = (currentPageGroup - 1) * 5 + 1;
      const endPage = Math.min(startPage + 4, this.pageCount);
      const pages = [];
      for (let i = startPage; i <= endPage; i++) {
        pages.push(i);
      }
      return pages;
    }
  },
  mounted() {
    this.fetchdata(this.menu)
  },
  watch : {
    isSupplier(){
      if (this.isSupplier === true) {
         this.menu = 'Supplier'
        this.fetchdata(this.menu)
      }

    }, 

    isBarang() {
      if (this.isBarang === true) 
      {
        this.menu = "Barang"
        this.fetchdata(this.menu)
      }

    }
  },
  methods : {
    fetchdata(menu) {
      let link = ''
      if (menu === 'Barang'){
        link = this.linkBarang
      } else {
        link = this.linkSupplier
      }
      const offset = (this.currentPage - 1) * this.limit;
      this.isLoading = true
            Axios.get(`${link}?limit=${this.limit}&offset=${offset}`, 
              { headers: {
                  'accept': '*/*',
                  'Content-Type': 'application/json',
                  'Authorization': 'Bearer ' + this.token // token adalah nilai dari authorization token yang diperoleh sebelumnya
              }})
            .then((resp) => {
              if (resp.status === 200) {
                if (this.menu === 'Supplier')
                {
                  this.updateItemNumbers();
                  this.detail = resp.data
                  this.listSupplier = this.detail.data
                  this.totalItems = this.detail.total_record
                  this.totalPage = this.detail.total_page
                  this.isLoading = false
                } else {
                  this.updateItemNumbers();
                  this.detail = resp.data
                  this.listbarang = this.detail.data
                  this.totalItems = this.detail.total_record
                  this.totalPage = this.detail.total_page
                  this.isLoading = false 
                }
              } else {
                this.gagalmodal = true
              }})
            .catch(err => console.error(err)) 
    },
    updateItemNumbers() {
      setTimeout(() => {
        const startIndex = (this.currentPage - 1) * this.limit + 1;
        this.listbarang.forEach((item, index) => {
          item.number = startIndex + index;
        });
        }, 500);
    },
    nextPage() {
      this.currentPage++;
      this.fetchdata(this.menu);
    },
    previousPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
        this.fetchdata(this.menu);
      }
    },
    changePage(pageNumber) {
      this.currentPage = pageNumber;
      this.fetchdata(this.menu);
    },
    changeLimit() {
      this.currentPage = 1;
       this.fetchdata(this.menu);
    },
    exportExcel() {
      const data = JSON.parse(JSON.stringify(this.listbarang)); 
      // let tbl = document.getElementById("xport")
      const wb = XLSX.utils.table_to_book(tbl);

      // Mengunduh file Excel
      XLSX.writeFile(wb, `Data ${this.menu} page ${this.currentPage}.xlsx`);
    },
    addBarang(menu){
      switch (menu) {
        case "Barang":
          router.push('/addbarang')
          break;
        case "Supplier":
        router.push('/addsupplier')
          break;
        default:
          break;
      }
    }, 
    updateData(item){
      if (this.isSupplier) {
        router.push({name : 'editsupplier', params : {id : item.id}})
        
      } else {
        router.push({name : 'editbarang', params : {id : item.id}})
      }
    },
    supplier(){
      this.isSupplier = true
      this.isBarang = false
    },
    barang(){
      this.isSupplier = false
      this.isBarang = true
    },
    logout(){
      localStorage.removeItem('token');
      router.push('/')
    }
  
  }}
</script>