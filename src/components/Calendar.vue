<template id='calendar'>
<div class="calendar-layout">
  <div class='left-column'>
    <div class='header'>
      <div class='column-company'>
        Company
      </div>
      <div class='column-client'>
        Client
      </div>
      <div class='column-contract'>
        Contract
      </div>
    </div>
    <div class='row'></div>
    <div class='full-row' v-for="contract in contracts" key="contract.xml_id">
      <div class='column-company'>
        {{ contract.organization }}
      </div>
      <div class='column-client'>
        {{ contract.contragent }}
      </div>
      <div class='column-contract'>
        {{ contract.name }}
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
      <div class='day' v-for='day in daysInMonth'>
        {{ day }}
      </div>
    </div>
    <div class='full-row' v-for="contract in contracts" key="'days|' + contract.xml_id">
      <div class="plan row">
        <div class='day' v-for='day in daysInMonth'></div>
        <div
          class="timeline timeline-plan"
          :class="'offset-' + contract.plan_work_dates.start_date + ' ' + 'width-' + (contract.plan_work_dates.end_date - contract.plan_work_dates.start_date)"
        ></div>
      </div>
      <div class="fact row">
        <div class='day' v-for='day in daysInMonth'></div>
        <div
          class="timeline timeline-fact"
          :class="'offset-' + contract.fact_work_dates.start_date + ' ' + 'width-' + (contract.fact_work_dates.end_date - contract.fact_work_dates.start_date)"
        ></div>
      </div>
      <div class="real row">
        <div class='day' v-for='day in daysInMonth' :class="{'day-with-events': (contract.jira_work_dates.indexOf(String(day)) > -1)}"></div>
      </div>
    </div>
  </div>
</div>
</template>


<script>
// Calendar data
const _daysInMonths = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
const _monthLabels = ['Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь'];
const _today = new Date();
const _todayComps = {
  year: _today.getFullYear(),
  month: _today.getMonth() + 1,
  day: _today.getDate(),
};

export default {
  props: ['contracts', 'events'],
  data() {
    return {
      month: _todayComps.month,
      year: _todayComps.year,
    };
  },
  computed: {
    // Our component exposes month as 1-based, but sometimes we need 0-based
    monthIndex() {
      return this.month - 1;
    },
    isLeapYear() {
      return (this.year % 4 === 0 && this.year % 100 !== 0) || this.year % 400 === 0;
    },
    // Day/month/year components for previous month
    previousMonthComps() {
      if (this.month === 1) return {
        days: _daysInMonths[11],
        month: 12,
        year: this.year - 1,
      }
      return {
        days: (this.month === 3 && this.isLeapYear) ? 29 : _daysInMonths[this.month - 2],
        month: this.month - 1,
        year: this.year,
      };
    },
    // Day/month/year components for next month
    nextMonthComps() {
      if (this.month === 12) return {
        days: _daysInMonths[0],
        month: 1,
        year: this.year + 1,
      };
      return {
        days: (this.month === 2 && this.isLeapYear) ? 29 : _daysInMonths[this.month],
        month: this.month + 1,
        year: this.year,
      };
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
  },
  methods: {
    moveThisMonth() {
      this.month = _todayComps.month;
      this.year = _todayComps.year;
    },
    moveNextMonth() {
      const {
        month,
        year
      } = this.nextMonthComps;
      this.month = month;
      this.year = year;
    },
    movePreviousMonth() {
      const {
        month,
        year
      } = this.previousMonthComps;
      this.month = month;
      this.year = year;
    },
    moveNextYear() {
      this.year++;
    },
    movePreviousYear() {
      this.year--;
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

$rowMinHeight: 50px

$dayColor: #3a3a3a
$dayBorder: solid 1px #aaaaaa
$dayBackgroundColor: white
$dayWidth: 30px
$dayHeight: 50px

$gridColumns: 31

@for $i from 1 through $gridColumns
  .offset-#{$i}
    position: absolute
    left: calc(#{$dayWidth} * #{$i})

@for $i from 1 through $gridColumns
  .width-#{$i}
    width: calc(#{$dayWidth} * #{$i})

*
  box-sizing: border-box

.calendar-layout
  display: flex

.calendar
  display: flex
  flex-direction: column

.header
  display: flex
  justify-content: stretch
  align-items: center
  min-height: $headerMinHeight
  color: $headerColor
  padding: $headerPadding
  border-width: $headerBorderWidth
  border-style: $headerBorderStyle
  border-color: $headerBorderColor
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
  justify-content: center
  align-items: center
  color: $dayColor
  background-color: $dayBackgroundColor
  border: $dayBorder
  // border-right: $dayBorder
  cursor: default

.row
  display: flex
  position: relative
  min-height: $rowMinHeight
  color: $dayColor
  // background-color: $dayBackgroundColor

  .timeline
    z-index: 100
    height: $dayHeight / 2
    top: 50%
    transform: translateY(-50%)

  .timeline.timeline-plan
    background-color: green

  .timeline.timeline-fact
    background-color: yellow

  .timeline.timeline-events
    background-color: red

.day-with-events
  background-color: red

.column-company
  width: 80px
.column-client
  width: 80px
.column-contract
  width: 120px

.full-row
  min-height: $rowMinHeight * 3 + 2px
  border-top: $dayBorder
  border-bottom: $dayBorder

.left-column .full-row
  display: flex

</style>
