<template>
  <div class="about">
    <table class="table">
      <thead>
        <tr>
          <th scope="col" v-for="column in columns" :key="column">{{column}}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in rows" :key="row">
          <th scope="row">{{row.Date}}</th>
          <td>{{row.Active}}</td>
          <td>{{row.Recovered}}</td>
          <td>{{row.Deaths}}</td>
          <td><button @click.prevent="info(row.Slug)" type="button" class="btn btn-primary">More info</button></td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

  <script lang="ts">
    import { Options, Vue } from 'vue-class-component';   
    import axios from 'axios';
    
    export default {
      name:'ActiveCasesView',
      props: {
        selected: String
      },
      data() {
        return {
          rows : [],
          columns: ['Day', 'Confirmed', 'Cured', 'Deaths'],
          isOpen: false,
          modalOpen: Boolean
        }
      },
      watch: {
        selected(event){
          this.rows;
        }
      },
      mounted() {
        axios.get(`https://api.covid19api.com/live/country/${this.selected}`).then(res => (this.rows = res.data.slice(0,10))),
        this.modalOpen = true;
        this.$emit('modalOpen', this.modalOpen);
      },
      methods: {
        test(event) {
          console.log('Active view',event)
          this.isOpen = true;
          axios.get(`https://api.covid19api.com/live/country/${event}`).then(res => (this.rows = res.data.slice(0,10)))
        },
        proba(){
          this.isOpen = true;
          this.rowsModal = [...this.rows.slice(0,10)]
        }
      }
    }
    </script>
    <style>
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

      .modal > div {
        background-color: antiquewhite;
        padding: 50px;
        border-radius: 10px;
      }

    </style>