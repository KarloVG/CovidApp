<template>
  <div class="content">
    <table class="table">
      <thead>
        <tr>
          <th scope="col" v-for="column in columns" :key="column">{{column}}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in rowsTable" :key="row">
          <th scope="row">{{row.Country}}</th>
          <td>{{row.TotalConfirmed}}</td>
          <td>{{row.TotalRecovered}}</td>
          <td>{{row.TotalDeaths}}</td>
          <td><button @click.prevent="getInfo(row.Slug)" type="button" class="btn btn-primary">More info</button></td>
        </tr>
      </tbody>
    </table>
    <div class="pages" v-if="pageRange.length > 0">
      <li class="list" v-for="page of pageRange" :key="page">
        <a href="#" :class="{active: isPageActive(page.page)}" @click.prevent="onClickPage(page.page)">{{page.page +
          1}}</a>
      </li>
    </div>
  </div>
  <div class="root">
    <teleport to="body">
      <div class="nes" v-if="isOpen">
        <div>
          <DataTable :rows="modalRows" :columns="countryColumns" :pageRange="modalPageRange" :rowsByPage="rowsByPage" />
          <button type="button" class="btn btn-primary" @click="isOpen = false">Close</button>
        </div>
      </div>
    </teleport>
  </div>
</template>

<script lang="ts">
  import { Options, Vue } from 'vue-class-component';
  import DataTable from '@/components/DataTable.vue'; // @ is an alias to /src
  import axios from 'axios';

  export default {
    data() {
      return {
        allRows: [],
        rowsTable: [],
        modalRows: [],
        pageRange: [],
        modalPageRange: [],
        rowsByPage: 8,
        currentPage: Number,
        columns: ['Country', 'Confirmed', 'Cured', 'Deaths', 'Info'],
        countryColumns: ['Day', 'Confirmed', 'Cured', 'Deaths'],
        isOpen: false,
      }
    },
    async mounted() {
      this.getSummary();
    },
    methods: {
      async getSummary(page) {
        await axios.get('https://api.covid19api.com/summary').then(res =>
          (this.allRows = res.data.Countries))
        this.onClickPage();
        this.pages(this.allRows, this.pageRange, this.rowsByPage);
      },
      async getInfo(event) {
        const data = await axios.get(`https://api.covid19api.com/live/country/${event}`);
        const rows = JSON.parse(JSON.stringify(data.data));
        rows.map((value) => {
          const date = new Date(value.Date).toLocaleDateString().split(',')[0];
          value.Date = date.replaceAll('/', '.')
        })
        this.modalRows = rows.slice(0, 10);
        this.pages(this.modalRows, this.modalPageRange, this.rowsByPage)
        this.isOpen = true;
      },
      pages(data, range, rows) {
        for (let i = 0; i <= data.length / rows; i++) {
          range.push({ page: i })
        }
      },
      onClickPage(page) {
        this.currentPage = page ? page : 0;
        const rowsTable = JSON.parse(JSON.stringify(this.allRows))
        const data =
          page ? rowsTable.slice(page * this.rowsByPage, page * this.rowsByPage + this.rowsByPage) :
            rowsTable.slice(0, this.rowsByPage);
        this.rowsTable = data;
      },
      isPageActive(page) {
        if (this.currentPage === page) {
          return true;
        }
      }
    },
    components: {
      DataTable
    }
  }
</script>
<style>
  .content {
    text-align: -webkit-center;
  }

  .table {
    width: 80% !important;
    border: 1px solid cadetblue;
  }

  .pages {
    width: 80%;
    display: flex;
    justify-content: end;
  }

  .list {
    list-style-type: none;
  }

  .list a {
    text-decoration: none !important;
    margin: 5px;
    color: #2c3e50;
  }

  .active {
    background-color: cornflowerblue;
    color: #ffffff !important;
    font-weight: bold;
    padding: 3px 8px;
  }

  .root {
    position: relative;
  }

  .nes {
    position: absolute;
    top: 0;
    left: 0;
    background-color: azure;
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .modal>div {
    background-color: antiquewhite;
    padding: 50px;
    border-radius: 10px;
  }
</style>