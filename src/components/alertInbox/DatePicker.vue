<template>
  <div class="wrapper">
    <div class="date-input" @click="toggleDatePicker">
      <span>{{ displayDate }}</span>
    </div>
    <BlawDatePicker
      :isOpen="isDatePickerOpen"
      :range="range"
      @date-selected="handleDateSelection"
    />
  </div>
</template>

<script>
import BlawDatePicker from './BlawDatePicker.vue';

export default {
  name: 'DatePicker',
  components: { BlawDatePicker },
  props: {
    range: {
      type: Boolean,
      default: true
    },
    modelValue: {
      type: Object,
      default: () => ({ start: null, end: null })
    }
  },
  data() {
    return {
      isDatePickerOpen: false,
      selectedDates: this.modelValue
    };
  },
  computed: {
    displayDate() {
      const { start, end } = this.selectedDates;

      if (start && end) {
        return `${this.formatDate(start)} - ${this.formatDate(end)}`;
      } else if (start) {
        return this.formatDate(start);
      }
      return 'DD/MM/YYYY - DD/MM/YYYY';
    }
  },
  watch: {
    modelValue(newVal) {
      this.selectedDates = newVal;
    }
  },
  methods: {
    toggleDatePicker() {
      this.isDatePickerOpen = !this.isDatePickerOpen;
    },
    handleDateSelection(dates) {
      // Apply +1 to the month before updating selectedDates
      const adjustedDates = {
        start: dates.start ? { ...dates.start, month: dates.start.month + 1 } : null,
        end: dates.end ? { ...dates.end, month: dates.end.month + 1 } : null,
      };

      this.selectedDates = adjustedDates;
      this.isDatePickerOpen = false;
      this.$emit('update:modelValue', adjustedDates);

      // Log the selected dates to match the display format
      console.log("Selected Start Date:", adjustedDates.start);
      console.log("Selected End Date:", adjustedDates.end);
    },
    formatDate(date) {
      if (!date) return '';
      const day = String(date.day).padStart(2, '0');
      const month = String(date.month).padStart(2, '0'); // Use the already adjusted month
      return `${day}/${month}/${date.year}`;
    }
  }
};
</script>

<style scoped>
.wrapper {
  text-align: center;
  margin: 20px;
}

.date-input {
  display: inline-block;
  padding: 8px;
  font-size: 16px;
  color: gray;
  border: 1px solid #cccccc;
  border-radius: 4px;
  cursor: pointer;
  min-width: 150px; 
  max-width: 100%; 
  text-align: left;
  background-color: white;
  white-space: nowrap; 
  overflow: hidden; 
  text-overflow: ellipsis; 
  box-sizing: border-box;
}

.date-input:focus-within {
  border-color: #0d9ddb;
}
</style>

