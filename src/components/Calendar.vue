<template>
  <div>
    <div class="calendar">
      <v-row justify="center" align="center">
        <v-col
          ><h3 id="date-title">{{ dateTitle }}</h3></v-col
        >
        <v-col></v-col>
        <v-col></v-col>
        <v-col></v-col>
        <v-col>
          <div class="month-select">
            <span @click="prevMonth"><v-btn outlined>前の月</v-btn></span>
            <span @click="nextMonth"><v-btn outlined>次の月</v-btn></span>
          </div>
        </v-col>
      </v-row>
      <br />
      <v-row justify="center" align="center">
        <table>
          <thead>
            <tr>
              <th
                v-for="week in weeks"
                :key="week.id"
                :class="{ sunday: week.id === 0 }"
              >
                {{ week.value }}
              </th>
            </tr>
            <tr v-for="(week, index) in calendars" :key="index">
              <td
                v-for="(day, index) in week"
                :key="index"
                :class="{ sunday: index === 0 }"
              >
                {{ day.date }}
              </td>
            </tr>
          </thead>
        </table>
      </v-row>
    </div>
  </div>
</template>

<style>
/* eslint-disable */
#date-title {
  margin-left: 60px;
}
.month-select {
  margin-right: 10px;
}
.sunday {
  color: red;
}
th {
  border: 1px solid #ddd;
  padding: 30px;
  text-align: center;
}
td {
  border: 1px solid #ddd;
  padding: 30px;
  text-align: center;
}
</style>

<script>
export default {
  data() {
    return {
      weeks: [
        { id: 0, value: "SUNDAY" },
        { id: 1, value: "MONDAY" },
        { id: 2, value: "TUESDAY" },
        { id: 3, value: "WEDNESDAY" },
        { id: 4, value: "THURSDAY" },
        { id: 5, value: "FRIDAY" },
        { id: 6, value: "SATURDAY" },
      ],
      today: new Date(),
      //new Date(2022, 8, 10) と指定したとき、Sep(9月)。月は、1月 = 0 からスタート。
      //月初の日.getDay()で曜日の取得
      startDay: new Date(
        this.date().getFullYear(),
        this.date().getMonth(),
        1
      ).getDay(),
      //タイムゾーンにおける月初の日
      startDate: new Date(this.date().getFullYear(), this.date().getMonth(), 1),
      //今月
      currentMonth: this.date().getMonth() + 1,

      currentYear: this.date().getFullYear(),
      //カレンダー表示されている先月の最後の日
      lastMonthEndDate: new Date(
        this.date().getFullYear(),
        this.date().getMonth(),
        0
      ).getDate(),
    };
  },
  computed: {
    dateTitle() {
      // jsのgetMonthが0-11なので1加算
      const month = this.currentMonth + 1;
      const result = this.currentYear + "年" + month + "月";
      return result;
    },
    calendars() {
      return this.renderCalendar();
    },
  },
  mounted() {
    console.log(this.today);
    console.log(this.currentMonth);
    console.log(this.startDay);
    console.log(this.startDate);
    console.log(this.lastMonthEndDate);
  },
  methods: {
    prevMonth() {
      this.currentMonth === 0 ? (this.currentMonth = 11) : this.currentMonth--;
      this.currentMonth === 11 && this.currentYear--;
      this.lastMonthEndDate = new Date(
        this.currentYear,
        this.currentMonth,
        0
      ).getDate();
      const dt = new Date(this.currentYear, this.currentMonth, 1);
      this.startDate = dt;
      this.startDay = dt.getDay();
      this.lastMonthEndDate = new Date(
        dt.getFullYear(),
        dt.getMonth(),
        0
      ).getDate();
    },
    nextMonth() {
      this.currentMonth === 11 ? (this.currentMonth = 0) : this.currentMonth++;
      this.currentMonth === 0 && this.currentYear++;
      this.lastMonthEndDate = new Date(
        this.currentYear,
        this.currentMonth,
        0
      ).getDate();
      const dt = new Date(this.currentYear, this.currentMonth, 1);
      this.startDate = dt;
      this.startDay = dt.getDay();
      this.lastMonthEndDate = new Date(
        dt.getFullYear(),
        dt.getMonth(),
        0
      ).getDate();
    },
    date() {
      return new Date();
    },
    year() {
      return this.date().getFullYear();
    },
    month() {
      return this.date().getMonth() + 1;
    },
    endDate() {
      return new Date(this.year(), this.month(), 0);
    },
    endDayCount() {
      return this.endDate().getDay();
    },
    renderCalendar() {
      const startDay = this.startDay;
      const currentDate = this.startDate;
      const lastMonthEndDate = this.lastMonthEndDate;
      const calendars = [];
      let lastDateCount = startDay - 1;
      for (let i = 0; i < 5; i++) {
        const weekRow = [];
        for (let day = 0; day < 7; day++) {
          if (i > 0 || (i === 0 && day >= startDay)) {
            weekRow.push({
              date: currentDate.getDate(),
            });
            currentDate.setDate(currentDate.getDate() + 1);
          } else {
            weekRow.push({
              // 曜日を使ってつじつま合わせ
              date: lastMonthEndDate - lastDateCount,
            });
            lastDateCount--;
          }
        }
        calendars.push(weekRow);
      }
      return calendars;
    },
  },
};
</script>