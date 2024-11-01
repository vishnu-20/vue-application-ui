<template>
    <div class="wrapper">
      <!-- Date Range Display Input -->
      <div class="date-input" @click="toggleDatePicker">
        <span v-if="selectedDates.start">
          {{ formatDate(selectedDates.start) }}
        </span>
        <span v-if="isRangeSelection"> - </span>
        <span v-if="isRangeSelection && selectedDates.end">
          {{ formatDate(selectedDates.end) }}
        </span>
        <span v-else-if="!selectedDates.start && !selectedDates.end">MM/DD/YYYY - MM/DD/YYYY</span>
      </div>
      
      <!-- BlawDatePicker component -->
      <BlawDatePicker
        :isOpen="isDatePickerOpen"
        :range="isRangeSelection"
        @date-selected="handleDateSelection"
      />
    </div>
  </template>
  
  <script>
import BlawDatePicker from './BlawDatePicker.vue';


  
  export default {
    components: { BlawDatePicker },
    data() {
      return {
        isDatePickerOpen: false,
        isRangeSelection: true, // Set to false for single date selection
        selectedDates: { start: null, end: null }
      };
    },
    methods: {
      toggleDatePicker() {
        this.isDatePickerOpen = !this.isDatePickerOpen;
      },
      handleDateSelection(dates) {
        this.selectedDates = dates;
        this.isDatePickerOpen = false; // Close date picker after selection
      },
      formatDate(date) {
        const month = String(date.month + 1).padStart(2, '0');
        const day = String(date.day).padStart(2, '0');
        return `${month}/${day}/${date.year}`;
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
    min-width: 200px;
    text-align: left;
    background-color: white;
  }
  .date-input:focus-within {
    outline: none;
    border-color: #0d9ddb;
  }
  </style>
  