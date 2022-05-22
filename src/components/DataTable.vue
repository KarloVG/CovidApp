<template>
  <div class="about">
    <h1>{{rows[0].Country}}</h1>
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
        </tr>
      </tbody>
    </table>
  </div>
</template>


<script lang="ts">
  import { Options, Vue } from 'vue-class-component';
  import axios from 'axios';

  export default {
    name: 'DataTable',
    props: {
      columns: [],
      pageRange: [],
      rowsByPage: Number,
      rows:
      {
        Country: String,
        Date: String,
        Active: Number,
        Recovered: Number,
        Deaths: Number
      }
    }

  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  h3 {
    margin: 40px 0 0;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
  }

  a {
    color: #42b983;
  }
</style>