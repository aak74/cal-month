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
  created () {
    this.contracts = []
    this.weekends = []
  },
  methods: {
    loadMonthData(payload) {
      console.log('monthChanged', payload);
      axios.get(
        '/efficiency/ajax.php?type=get_calendar_contract&year='
          + payload.year
          + '&month=' + payload.month
      ).then(response => {
        console.log('response', response);

      })
      // this.weekends = mock.weekends
      // this.contracts = mock.contracts
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
