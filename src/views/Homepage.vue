<template>
  <div class="about">
    <h1>This is an about page</h1>
    <button @click.prevent="supplier"> Supplier</button>
<div class="ma-2 pa-3 border rounded">
  <button @click.prevent="addBarang"> Add Barang</button>
  <v-container>
      <v-table
        fixed-header
        height="600px"
        id="tbl"
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
            <td class="pa-2"> <button @click.prevent="updateData(item)" class="border pa-2 rounded">Update</button> <button class="border pa-2 rounded">Hapus</button></td>
          </tr>
        </tbody>
        <div>
        </div>
      </v-table>

      <div>
        limit 
        <select id="limit" v-model="limit" @change.prevent="changeLimit">
          <option v-for="option in limitOptions" :key="option" :value="option">{{ option }}</option>
        </select>
         <button :disabled="currentPage === 1" @click.prevent="previousPage">Previous</button>
        <div>
          <button v-for="pageNumber in pageCount" :key="pageNumber" @click="changePage(pageNumber)">
            {{ pageNumber }}
          </button>
          <button :disabled="currentPage === pageCount" @click.prevent="nextPage">Next</button>
        </div>
          
      </div>
      
      <div>
        <button class="button btn-primary" @click.prevent="exportExcel">Export to Excel</button>
      </div>
  </v-container>
</div>
  </div>
</template>
<script>
import router from '@/router'
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
      totalPage: 0
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
    this.fetchdata()
  },
  methods : {
    fetchdata() {
      const offset = (this.currentPage - 1) * this.limit;
             Axios.get(`http://159.223.57.121:8090/barang/find-all?limit=${this.limit}&offset=${offset}`, 
              {
                headers: {
                  'accept': '*/*',
                  'Content-Type': 'application/json',
                  'Authorization': 'Bearer ' + this.token // token adalah nilai dari authorization token yang diperoleh sebelumnya
              }
            })
            .then((resp) => {
              if (resp.status === 200){
                this.updateItemNumbers();
                this.detail = resp.data
                this.listbarang = this.detail.data
                this.totalItems = this.detail.total_record
                this.totalPage = this.detail.total_page
              } else {

              }
            }
            )
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
    async nextPage() {
      this.currentPage++;
      await this.fetchdata();
    },
    async previousPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
        await this.fetchdata();
      }
    },
    changePage(pageNumber) {
      this.currentPage = pageNumber;
      this.fetchdata();
    },
    changeLimit() {
      this.currentPage = 1;
      this.fetchdata();
    },
    exportExcel() {
      const data = JSON.parse(JSON.stringify(this.listbarang)); 
      document.getElementById("xport")
      const wb = XLSX.utils.table_to_book(tbl);

      // Mengunduh file Excel
      XLSX.writeFile(wb, "HTMLFlicker.xlsx");
    },
    addBarang(){
      router.push('/addbarang')
    }, 
    updateData(item){
      console.log(item.id)
      // let itemdetail = item.id  
      router.push({name : 'editbarang', params : {id : item.id}})
    },
    supplier(){
      router.push('/supplier')
    }
  
  }}
</script>