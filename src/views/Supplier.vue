<template>
  <div class="about">
    <h1>This is Supplier Page</h1>
    <div>
      <button class="btn" @click.prevent="gotoaddpage"
      > Add Supplier</button>
    </div>
    <v-container>
        <v-table
          fixed-header
          height="600px"
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
        
        <select id="limit" v-model="limit" @change="changeLimit">
          <option v-for="option in limitOptions" :key="option" :value="option">{{ option }}</option>
        </select>
        <button :disabled="currentPage === 1" @click="previousPage">Previous</button>
        <button v-for="pageNumber in pageCount" :key="pageNumber" @click="changePage(pageNumber)">
          {{ pageNumber }}
        </button>
        <button :disabled="currentPage === pageCount" @click="nextPage">Next</button>
    </v-container>
  </div>
</template>
<script>
import router from '@/router'
import Axios from 'axios'

export default {
  name: 'Supplier',
  components: {
  },
  data() {
    return {
      detaildata : '',
      password : '',
      token: localStorage.getItem('token') || null,
      detail : {},
      listSupplier : [],
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
             Axios.get(`http://159.223.57.121:8090/supplier/find-all?limit=${this.limit}&offset=${offset}`, 
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
                this.listSupplier = this.detail.data
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
        this.listSupplier.forEach((item, index) => {
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
    gotoaddpage() {
      router.push('/addsupplier')
    },
    updateData(item){
      console.log(item.id)
      // let itemdetail = item.id  
      router.push({name : 'editbarang', params : {id : item.id}})
    }
  }
  }
</script>