<template>
  <div id="app">
    <calendar :contracts="contracts" :weekends="weekends" @monthChanged="loadMonthData"/>
  </div>
</template>

<script>
import Calendar from './components/Calendar'
import mock from './mock/data'
const axios = require('axios')


const state = require('./state')

export default {
  name: 'app',
  components: {
    Calendar
  },
  data () {
    return {
      contracts: this.contracts,
      weekends: this.weekends
    }
  },
  // computed: {
  //   contracts () {
  //     return this.data.contracts
  //   },
  //   weekends () {
  //     return this.data.weekends
  //   },
  // },
  created () {
    this.contracts = []
    this.weekends = []
  },
  methods: {
    loadMonthData(payload) {
      // axios.get(
      //   '/efficiency/ajax.php', {
      //     type: 'get_calendar_contract',
      //     year: this.year,
      //     month: this.month
      //   }
      // ).then(response => {
      //   console.log('response', response);
      //
      // })
      this.weekends = mock.weekends
      this.contracts = mock.contracts
      console.log('monthChanged', payload);
    },
  }
}
</script>

<style lang="sass">
$fontFamily: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen", "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue", "Helvetica", "Arial", sans-serif

#app
  display: flex
  font-family: $fontFamily
  padding: 20px

</style>
