<template>
  <div class="cal-container">
    <!-- Header, select month, show month name and select todays month -->
    <div class="cal-header">
      <div class="group-1">
        <button class="arw-btn" @click="prevMonth">
          <i class="arrow left"></i>
        </button>
        <button class="arw-btn" @click="nextMonth">
          <i class="arrow right"></i>
        </button>
      </div>
      <div class="group-2">
        <h2 style="font-weight: unset">
          {{ toMonthString(selectedDate) }}
        </h2>
      </div>
      <div class="group-3">
        <div>
          <button class="today-button" @click="todaysMonth">
            Today's Month
          </button>
        </div>
      </div>
    </div>
    <div class="cal-seven">
      <!-- Calendar days monday-sunday, 7 days -->
      <div class="dayname">Mon</div>
      <div class="dayname">Tue</div>
      <div class="dayname">Wed</div>
      <div class="dayname">Thu</div>
      <div class="dayname">Fri</div>
      <div class="dayname">Sat</div>
      <div class="dayname">Son</div>
      <div
        class="other-month-days cal-dates"
        v-for="(day, index) in lastMonthDays"
        :key="index + 'beforeThisMonthDays'"
      >
        {{ day }}
      </div>
      <div
        class="cal-dates"
        v-for="(day, index) in days"
        :key="index + 'selectedMonthDays'"
        :class="{ 'is-today': isToday(day.date) }"
      >
        {{ showDate(day.date) }}
      </div>
      <div
        class="other-month-days cal-dates"
        v-for="(day, index) in nextMonthDays"
        :key="index + 'afterThisMonthDays'"
      >
        {{ day }}
      </div>
    </div>
    <!-- Calendar end -->
  </div>
</template>
<script>
export default {
  props: [],
  data() {
    return {
      today: new Date(),
      selectedDate: new Date(),
      selectedMonth: new Date().getMonth(),
      lastMonthDays: [],
      days: [],
      nextMonthDays: [],

      // If you get an array of objects from database, one way to save and render them is to do it with this array (see more in loadDatabaseMonth)
      // daysDatabase: [],
    };
  },
  methods: {
    showDate: function (date) {
      var day = new Date(date).getDate();
      return day;
    },
    drawMonth(d) {
      const date = d;
      date.setHours(0, 0, 0);
      const year = date.getFullYear();
      const month = date.getMonth();
      const hours = date.getHours();

      // Make an array with days based on selected month
      const daysInSelectedMonth = new Date(year, month + 1, 0).getDate();
      for (let i = 1; i < daysInSelectedMonth + 1; i++) {
        this.days.push({
          date: this.toCustomISO(new Date(year, month, i, hours)),
        });
      }

      // Make array of divs based on the month before
      const dateYearAndMonth = new Date(year, month);
      const firstDay = (dateYearAndMonth.getDay() + 6) % 7;
      const lastDateLastMonth = new Date(year, month, 0).getDate();
      let endingDatesLastMonth = lastDateLastMonth - firstDay + 1;
      for (let i = 0; i < firstDay; i++) {
        this.lastMonthDays.push(endingDatesLastMonth);
        endingDatesLastMonth++;
      }

      // Make array of divs based on the next month
      const totalDays = this.days.length + this.lastMonthDays.length;
      for (let i = 0; i < 42 - totalDays; i++) {
        this.nextMonthDays.push(i + 1);
      }
      // Alternative if you want to load in specific info from database:
      // this.loadDatabaseMonth();
    },
    nextMonth: function () {
      this.resetMonth();
      this.selectedDate.setMonth(this.selectedDate.getMonth() + 1); //må bruke setMonth
      this.selectedDate.setDate(1);
      this.drawMonth(this.selectedDate);
    },
    prevMonth: function () {
      this.resetMonth();
      this.selectedDate.setMonth(this.selectedDate.getMonth() - 1); //må bruke setMonth
      this.selectedDate.setDate(1);
      this.drawMonth(this.selectedDate);
    },
    todaysMonth() {
      this.resetMonth();
      this.selectedDate = new Date();
      this.drawMonth(new Date());
    },
    resetMonth() {
      this.lastMonthDays = [];
      this.days = [];
      this.nextMonthDays = [];
    },
    toMonthString(date) {
      const d = date;
      const options = {
        month: "long",
        year: "numeric",
      };
      // Select your local string format here. Currently "en-GB", english. See more at: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleDateString
      return d.toLocaleDateString("en-GB", options);
    },
    isToday(date) {
      var today = new Date().toDateString();
      var sjekkdato = new Date(date).toDateString();

      if (today == sjekkdato) {
        return true;
      } else {
        return false;
      }
    },
    toCustomISO(d) {
      var z = (n) => ("0" + n).slice(-2);

      return (
        d.getFullYear() +
        "-" +
        z(d.getMonth() + 1) +
        "-" +
        z(d.getDate()) +
        "T00:00:00"
      );
    },

    // DATABASE-SPECIFIC FUNCTIONS:
    splitDate: function (date) {
      if (date) {
        const d = this.toCustomISO(new Date(date));
        const splitDate = d.split("T");
        return splitDate[0];
      } else {
        this.addMinimumDate();
      }
    },
    loadDatabaseMonth: function () {
      const that = this;
      fetch(
        "https://my-json-database.com/month/test?date=" +
          that.splitDate(that.toCustomISO(that.selectedDate)),
        {
          cache: "no-cache",
        }
      ).then(function (response) {
        response.json().then(function (result) {
          that.daysDatabase = result;

          for (let i = 0; i < this.daysDatabase.length; i++) {
            let extractIndexFromDatabaseToDays = that.days.findIndex(
              (el) => el.date === that.daysDatabase[i].date
            );
            if (extractIndexFromDatabaseToDays) {
              that.days.splice(
                extractIndexFromDatabaseToDays,
                1,
                that.daysDatabase[i]
              );
            }
          }
        });
      });
    },
  },
  created() {
    this.drawMonth(this.today);
  },
};
</script>

<style scoped>
* {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
body,
html {
  margin: 0;
  padding: 0;
}

/* CALENDAR */
.cal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
}
.cal-container {
  border: 1px solid rgb(219, 219, 219);
  padding: 15px;
}
.cal-seven {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  grid-gap: 1px;
  border: 1px solid rgb(219, 219, 219);
}
.cal-seven div {
  background-color: white;
}
.cal-dates {
  min-height: 100px;
  padding: 4px;
  text-align: right;
  font-size: 0.9rem;
}
.cal-seven .dayname {
  max-height: 20px;
  text-align: center;
}
.cal-seven .is-today {
  background-color: rgb(138, 29, 29);
  color: white;
}
.other-month-days {
  opacity: 0.6;
  padding: 4px;
}
.cal-seven:not(.dayname) {
  background-color: rgb(180, 180, 180);
}

/* BUTTONS */
button {
  cursor: pointer;
}
.today-button {
  height: 37px;
  padding: 10px 15px;
  color: white;
  border: 1px solid rgb(138, 29, 29);
  border-radius: 5px;
  background-color: rgb(138, 29, 29);
  margin-right: 15px;
}
.arw-btn:hover,
.today-button:hover {
  border: 1px solid rgb(116, 20, 20);
  background-color: rgb(163, 46, 46);
}
.arw-btn {
  width: 40px;
  height: 37px;
  border: 1px solid rgb(105, 20, 20);
  background-color: rgb(138, 29, 29);
}
.arw-btn:first-child {
  margin-left: 20px;
  border-radius: 5px 0 0 5px;
  padding: 10px 11px 10px 15px;
}
.arw-btn:nth-child(2) {
  padding: 10px 15px 10px 11px;
  border-radius: 0 5px 5px 0;
}
.arrow {
  border: solid white;
  border-width: 0 2px 2px 0;
  display: inline-block;
  padding: 3px;
}
.right {
  transform: rotate(-45deg);
  -webkit-transform: rotate(-45deg);
}
.left {
  transform: rotate(135deg);
  -webkit-transform: rotate(135deg);
}
</style>
