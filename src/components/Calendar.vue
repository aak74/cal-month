<template id='calendar'>
<div class="calendar-layout">
  <div class='sidebar'>
    <div class='sidebar-header'>
      <div class='column-number'>№</div>
      <div class='column-company'>Компания</div>
      <div class='column-client'>Клиент</div>
      <div class='column-contract'>Договор</div>
    </div>
    <div class="contracts">
      <div class='row' v-for="(contract, key) in contracts" key="contract.xml_id">
        <div class='column-number'>{{ key + 1 }}</div>
        <div class='column-company'>{{ contract.organization }}</div>
        <div class='column-client'>{{ contract.contragent }}</div>
        <div class='column-contract'>{{ contract.name }}</div>
      </div>
    </div>
  </div>
  <div class='calendar'>
    <div class='header'>
      <a class='arrow' @click='movePreviousYear'>&laquo;</a>
      <a class='arrow' @click='movePreviousMonth'>&lsaquo;</a>
      <span class='title' @click='moveThisMonth'>
        {{ header.label }}
      </span>
      <a class='arrow' @click='moveNextMonth'>&rsaquo;</a>
      <a class='arrow' @click='moveNextYear'>&raquo;</a>
    </div>
    <div class='row month-days'>
      <div
        v-for='day in days'
        class='day day-header'
        :class="{'day-weekend': (weekends.indexOf(String(day.day)) > -1), 'today': day.isToday}"
      ><span class="day-number">{{ day.day }}</span><span class="day-weekday">{{ day.weekday }}</span></div>
    </div>
    <div class='full' v-for="contract in contracts" key="'days|' + contract.xml_id">
      <div class="real row">
        <div
          class='day'
          v-for='day in days'
          :class="{
            'day-with-events': (contract.jira_work_dates.indexOf(String(day.day)) > -1),
            'day-weekend': (weekends.indexOf(String(day.day)) > -1),
            'today': day.isToday}"
          ></div>
        <div
        class="timeline timeline-plan"
        :class="'offset-' + contract.plan_work_dates.start_date + ' ' + 'width-' + (contract.plan_work_dates.end_date - contract.plan_work_dates.start_date)"
        ></div>
        <div
        class="timeline timeline-fact"
        :class="'offset-' + contract.fact_work_dates.start_date + ' ' + 'width-' + (contract.fact_work_dates.end_date - contract.fact_work_dates.start_date)"
        ></div>
      </div>
    </div>
  </div>
</div>
</template>


<script>
// Calendar data
const _daysInMonths = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
const _dayLabels = ['вс', 'пн', 'вт', 'ср', 'чт', 'пт', 'сб', 'вс'];
const _monthLabels = ['Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь'];
const _today = new Date();
const _todayComps = {
  year: _today.getFullYear(),
  month: _today.getMonth() + 1,
  day: _today.getDate(),
};

export default {
  props: ['contracts', 'weekends'],
  data() {
    return {
      month: _todayComps.month,
      year: _todayComps.year,
    };
  },
  created () {
    this.$emit('monthChanged', {year: this.year, month: this.month})
  },
  computed: {
    // Our component exposes month as 1-based, but sometimes we need 0-based
    monthIndex() {
      return this.month - 1;
    },
    isLeapYear() {
      return (this.year % 4 === 0 && this.year % 100 !== 0) || this.year % 400 === 0;
    },
    // State for calendar header (no dependencies yet...)
    months() {
      return _monthLabels.map((ml, i) => ({
        label: ml,
        number: i + 1,
      }));
    },
    header() {
      const month = this.months[this.monthIndex];
      return {
        month: month,
        year: this.year.toString(),
        label: month.label + ', ' + this.year,
      };
    },
    // Returns number of days in the current month
    daysInMonth() {
      // Check for February in a leap year
      if (this.month === 2 && this.isLeapYear) return 29;
      // ...Just a normal month
      return _daysInMonths[this.monthIndex];
    },
    days() {
      let days = []
      for (var i = 0; i < this.daysInMonth; i++) {
        let dt = new Date(this.year, this.monthIndex, i + 1)
        console.log('days', dt);

        const isToday = (_todayComps.year === dt.getFullYear()
          && _todayComps.month === dt.getMonth() + 1
          && _todayComps.day === dt.getDate())

        days.push({day: i+1, weekday: _dayLabels[dt.getDay()], isToday})
      }
      return days
    },
  },
  methods: {
    moveThisMonth() {
      this.month = _todayComps.month
      this.year = _todayComps.year
      this.$emit('monthChanged', {year: this.year, month: this.month})
    },
    moveNextMonth() {
      if (this.month < 12) {
        this.month++
      } else {
        this.month = 1
        this.year++
      }
      this.$emit('monthChanged', {year: this.year, month: this.month})
    },
    movePreviousMonth() {
      if (this.month > 1) {
        this.month--
      } else {
        this.month = 12
        this.year--
      }
      this.$emit('monthChanged', {year: this.year, month: this.month})
    },
    moveNextYear() {
      this.year++
      this.$emit('monthChanged', {year: this.year, month: this.month})
    },
    movePreviousYear() {
      this.year--
      this.$emit('monthChanged', {year: this.year, month: this.month})
    },
  },
}
</script>

<style lang="sass">
$fontFamily: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen", "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue", "Helvetica", "Arial", sans-serif
$themeColor: #ff7a58

$headerPadding: 0.5rem 1rem
$headerBorderWidth: 1px
$headerBorderStyle: solid
$headerBorderColor: #aaaaaa
$headerBackground: $themeColor
$headerColor: white
$headerMinHeight: 60px

$dayColor: #3a3a3a
$dayBorder: solid 1px #aaaaaa
$dayBackgroundColor: white
$todayBackgroundColor: red
$dayWidth: 45px
$dayHeight: 57px
$rowMinHeight: $dayHeight
$timelineHeight: $dayHeight / 3

$gridColumns: 31

@for $i from 1 through $gridColumns
  .offset-#{$i}
    position: absolute
    left: calc(#{$dayWidth} * (#{$i} - 1))

@for $i from 1 through $gridColumns
  .width-#{$i}
    width: calc(#{$dayWidth} * (#{$i} + 1))

*
  box-sizing: border-box

.header
  display: flex
  justify-content: stretch
  align-items: center
  min-height: $headerMinHeight
  // color: $headerColor
  padding: $headerPadding
  // background-color: $headerBackground


.calendar-layout
  display: flex

.calendar
  display: flex
  flex-direction: column

  .header
    // display: flex
    // justify-content: stretch
    // align-items: center
    // min-height: $headerMinHeight
    color: $headerColor
    // padding: $headerPadding
    background-color: $headerBackground

    =pointer()
      cursor: pointer
      &:hover
        color: #dcdcdc

    .arrow
      +pointer
      padding: 0 0.4em 0.2em 0.4em
      font-size: 1.8rem
      font-weight: 500
      user-select: none
      flex-grow: 0

    .title
      +pointer
      flex-grow: 1
      font-size: 1.2rem
      text-align: center

.day
  width: $dayWidth
  height: $dayHeight
  min-height: $dayHeight
  justify-content: center
  align-items: center
  color: $dayColor
  background-color: $dayBackgroundColor
  // border-bottom: $dayBorder
  border-right: $dayBorder
  cursor: default

.day.today
  background-color: $todayBackgroundColor

.day-header
  text-align: center

  span
    display: block

  span.day-weekday
    font-size: 80%

.row
  display: flex
  position: relative
  min-height: $rowMinHeight
  // min-height: $rowMinHeight
  color: $dayColor
  border-bottom: $dayBorder
  // background-color: $dayBackgroundColor

  .timeline
    z-index: 100

  .timeline.timeline-plan
    height: $dayHeight / 3
    background-color: green

  .timeline.timeline-fact
    top: 33%
    height: $dayHeight / 3
    background-color: yellow

.day-with-events
  border-bottom: solid red $dayHeight / 3 - 1px

.day-weekend
  background: #e7eef1

.column-number,
.column-company,
.column-client,
.column-contract
  border-right: $dayBorder

.column-number
  width: 30px
  text-align: center
.column-company
  width: 100px
.column-client
  width: 100px
.column-contract
  width: 160px

.sidebar
  border-left: $dayBorder
  border-top: $dayBorder
  .row
    min-height: $rowMinHeight + 1px
// .sidebar .full-row
.sidebar-header
  display: flex
  height: $rowMinHeight + $headerMinHeight
  border-bottom: $dayBorder

  .column-company,
  .column-client,
  .column-contract
    text-align: center

</style>
