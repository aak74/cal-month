<template>
  <div id="app">
    <calendar :contracts="contracts"/>
  </div>
</template>

<script>
import Calendar from './components/Calendar'
import data from './mock/data'

export default {
  name: 'app',
  components: {
    Calendar
  },
  data () {
    return {
      contracts: data
    }
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
