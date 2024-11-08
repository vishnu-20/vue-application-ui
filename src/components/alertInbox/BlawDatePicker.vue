<template>
  <div class="date-picker" v-show="isOpen">
    <div class="header">
      <span @click="switchView" class="header-label">{{ label }}</span>
    </div>
    
    <div v-if="currentView === 'DAY_VIEW'">
      <div class="days-header">
        <span v-for="day in daysOfWeek" :key="day">{{ day }}</span>
      </div>
      <div class="calendar-grid">
        <div v-for="item in calendarItems" :key="item.value" 
             :class="['calendar-day', { 'today': isToday(item), 'selected': isSelected(item), 'in-range': isInRange(item) }]"
             @click="selectDate(item)">
          {{ item.label }}
        </div>
      </div>
    </div>
    
    <div v-else-if="currentView === 'MONTH_VIEW'">
      <div class="calendar-grid months-grid">
        <div v-for="(month, index) in months" :key="index" 
             class="calendar-month" 
             @click="selectMonth(index)">
          {{ month }}
        </div>
      </div>
    </div>

    <div v-else-if="currentView === 'YEAR_VIEW'">
      <div class="calendar-grid years-grid">
        <div v-for="year in years" :key="year" 
             class="calendar-year" 
             @click="selectYear(year)">
          {{ year }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'BlawDatePicker',
  props: {
    isOpen: Boolean,
    range: Boolean
  },
  data() {
    return {
      currentView: 'DAY_VIEW',
      today: new Date(),
      current: { year: new Date().getFullYear(), month: new Date().getMonth(), day: new Date().getDate() },
      selected: { start: null, end: null },
      daysOfWeek: ["SUN", "MON", "TUE", "WED", "THU", "FRI", "SAT"],
      months: ["JAN", "FEB", "MAR", "APR", "MAY", "JUNE", "JULY", "AUG", "SEP", "OCT", "NOV", "DEC"],
      yearStart: new Date().getFullYear() - 5
    };
  },
  computed: {
    label() {
      if (this.currentView === 'DAY_VIEW') return `${this.months[this.current.month]} ${this.current.year}`;
      if (this.currentView === 'MONTH_VIEW') return `${this.current.year}`;
      return `${this.yearStart} - ${this.yearStart + 11}`;
    },
    calendarItems() {
      if (this.currentView === 'DAY_VIEW') return this.populateDays();
      if (this.currentView === 'MONTH_VIEW') return this.months;
      return this.populateYears();
    },
    years() {
      return Array.from({ length: 12 }, (_, i) => this.yearStart + i);
    }
  },
  methods: {
    populateDays() {
      const startOfMonth = new Date(this.current.year, this.current.month, 1);
      const daysInMonth = new Date(this.current.year, this.current.month + 1, 0).getDate();
      const prevDaysInMonth = new Date(this.current.year, this.current.month, 0).getDate();
      const startDay = startOfMonth.getDay();
      let calendarItems = [];
      
      for (let i = 0; i < startDay; i++) {
        calendarItems.push({ label: prevDaysInMonth - startDay + i + 1, value: null });
      }
      for (let i = 1; i <= daysInMonth; i++) {
        calendarItems.push({ label: i, value: i });
      }
      while (calendarItems.length % 7 !== 0) {
        calendarItems.push({ label: calendarItems.length - daysInMonth + 1, value: null });
      }
      return calendarItems;
    },
    populateYears() {
      return Array.from({ length: 12 }, (_, i) => this.yearStart + i);
    },
    isToday(item) {
      return item.value && this.today.getDate() === item.value && this.today.getMonth() === this.current.month && this.today.getFullYear() === this.current.year;
    },
    isSelected(item) {
      if (!item.value) return false;
      return (this.selected.start && item.value === this.selected.start.day && this.current.month === this.selected.start.month && this.current.year === this.selected.start.year)
        || (this.range && this.selected.end && item.value === this.selected.end.day && this.current.month === this.selected.end.month && this.current.year === this.selected.end.year);
    },
    isInRange(item) {
      if (!this.range || !this.selected.start || !this.selected.end || !item.value) return false;
      const date = new Date(this.current.year, this.current.month, item.value);
      const startDate = new Date(this.selected.start.year, this.selected.start.month, this.selected.start.day);
      const endDate = new Date(this.selected.end.year, this.selected.end.month, this.selected.end.day);
      return date >= startDate && date <= endDate;
    },
    selectDate(item) {
      if (!item.value) return;
      const date = { day: item.value, month: this.current.month, year: this.current.year };
      if (!this.range || !this.selected.start || (this.selected.start && this.selected.end)) {
        this.selected.start = date;
        this.selected.end = null;
      } else {
        this.selected.end = date;
      }
      this.$emit('date-selected', { start: this.selected.start, end: this.selected.end });
    },
    selectMonth(month) {
      this.current.month = month;
      this.currentView = 'DAY_VIEW';
    },
    selectYear(year) {
      this.current.year = year;
      this.currentView = 'MONTH_VIEW';
    },
    switchView() {
      if (this.currentView === 'DAY_VIEW') {
        this.currentView = 'MONTH_VIEW';
      } else if (this.currentView === 'MONTH_VIEW') {
        this.currentView = 'YEAR_VIEW';
      }
    }
  }
};
</script>

<style scoped>
.date-picker {
  width: 360px;
  border: 1px solid #bbbbbb;
  background-color: #F0F3F7;
}
.header {
  display: flex;
  justify-content: center;
  padding: 10px;
  background-color: #0D9DDB;
  color: white;
}
.header-label {
  cursor: pointer;
  font-weight: bold;
}
.days-header, .calendar-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  text-align: center;
}
.months-grid, .years-grid {
  grid-template-columns: repeat(3, 1fr);
}
.calendar-day, .calendar-month, .calendar-year {
  padding: 10px;
  cursor: pointer;
}
.today {
  border: 1px solid #0D9DDB;
}
.selected {
  background-color: #0D9DDB;
  color: white;
}
.in-range {
  background-color: #72c6ea;
}
</style>
