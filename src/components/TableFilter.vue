<template>
    <div class="filter-panel">
      <h3 class="filter-title">Filters</h3>
      <div class="filter-group-search">
        <input
          type="text"
          v-model="filterCriteria.keywords"
          placeholder="Search Keywords"
          @input="emitFilter"
          class="filter-input"
        />
        <button @click="emitFilter" class="search-button">
          <i class="search-icon"></i>
        </button>
      </div>
  
      <div class="filter-group">
        <input
          type="text"
          v-model="filterCriteria.dateRange"
          placeholder="MM/DD/YYYY — MM/DD/YYYY"
          class="filter-input"
        />
      </div>
  
      <div class="filter-group">
        <label for="show" class="filter-label">Show:</label>
        <select id="show" v-model="filterCriteria.show" @change="emitFilter" class="filter-select">
          <option value="">All</option>
          <option value="Docket Searches">Docket Searches</option>
          <option value="Legal Searches">Legal Searches</option>
        </select>
      </div>
  
      <!-- Dynamic Type Checkboxes -->
      <div class="filter-group">
        <p class="filter-label">Type</p>
        <div v-for="type in uniqueTypes" :key="type" class="filter-checkbox">
          <input type="checkbox" :id="type" :value="type" v-model="filterCriteria.type" @change="emitFilter" />
          <label :for="type">{{ type }}</label>
        </div>
      </div>
  
      <div class="filter-group">
        <p class="filter-label">Tag</p>
        <div class="filter-checkbox">
          <input type="checkbox" id="basicTag" value="basictag" v-model="filterCriteria.tags" @change="emitFilter" />
          <label for="basicTag">basictag</label>
        </div>
        <div class="filter-checkbox">
          <input type="checkbox" id="testTag" value="testtag" v-model="filterCriteria.tags" @change="emitFilter" />
          <label for="testTag">testtag</label>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      tableData: {
        type: Array,
        required: true,
      },
    },
    data() {
      return {
        filterCriteria: {
          keywords: '',
          dateRange: '',
          show: '',
          type: [],
          tags: [],
        },
      };
    },
    computed: {
      uniqueTypes() {
        const types = this.tableData.map(item => item.type);
        return [...new Set(types)]; // Get unique types from tableData
      },
    },
    methods: {
      emitFilter() {
        this.$emit('filter-changed', this.filterCriteria);
      },
    },
  };
  </script>
  
  <style scoped lang="scss">
  .filter-panel {
    width: 250px;
    padding: 15px;
    border: 1px solid #dcdcdc;
    border-radius: 4px;
    background-color: #ffffff;
    box-shadow: 0 2px 5px 0 rgba(0, 0, 0, 0.3);
    font-family: "Open Sans", sans-serif;
    color: #333;
  }
  
  .filter-title {
    font-size: 16px;
    font-weight: bold;
    margin-bottom: 15px;
    color: #333;
  }
  
  .filter-group {
    margin-bottom: 15px;
  }
  
  .filter-group-search {
    margin-bottom: 15px;
    display: flex;
  }
  
  .filter-input {
    width: calc(100% - 20px);
    padding: 8px;
    font-size: 14px;
    border: 1px solid #c5cacd;
    border-radius: 4px;
    margin-right: 5px;
  }
  
  .search-button {
    background-color: #4caf50;
    border: none;
    padding: 8px;
    cursor: pointer;
    border-radius: 4px;
    color: #fff;
  }
  
  .search-icon::before {
    content: "🔍";
    font-size: 14px;
  }
  
  .filter-select {
    width: 100%;
    padding: 8px;
    font-size: 14px;
    border: 1px solid #c5cacd;
    border-radius: 4px;
  }
  
  .filter-label {
    font-size: 14px;
    font-weight: bold;
    margin-bottom: 5px;
    color: #333;
  }
  
  .filter-checkbox {
    display: flex;
    align-items: center;
    margin-bottom: 5px;
  
    input[type="checkbox"] {
      margin-right: 8px;
    }
  }
  </style>
  