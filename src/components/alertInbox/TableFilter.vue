<template>
  <div class="SearchResultsFilters">
    <h2 class="filter">Filters</h2>
    <div class="owner-label">Search with Keyword</div>
    <!-- <TextInput
      class="search-input"
      v-model="filterCriteria.localSearchTerm"
      placeholder="Search Keyword"
      :icon="search24"
    />
    <Pill
    class="search-input"
    v-for="(pill, index) in terms"
    :disabled="pill.disabled"
    :hide-remove="pill.hideRemove"
    :key="index"
    :label="pill.label"
    @remove="removeTerm(index)"
  /> -->
  <div class="filter-group">
      <p class="filter-label">Types</p>
      <div v-for="tag in uniqueTypes" :key="tag" class="filter-checkbox">
        <input type="checkbox" :id="tag" :value="tag" v-model="filterCriteria.tags" @change="emitFilter" />
        <label :for="tag">{{ tag }}</label>
      </div>
      </div>
    <div class="filter-group">
      <p class="filter-label">Tags</p>
      <div v-for="tag in uniquetags" :key="tag" class="filter-checkbox">
        <input type="checkbox" :id="tag" :value="tag" v-model="filterCriteria.tags" @change="emitFilter" />
        <label :for="tag">{{ tag }}</label>
      </div>
    </div>
  </div>
</template>

<script>
// import { TextInput, Pill } from '@blaw/blaw-ui-generic-components';
import search24 from "@fishtank/icons/dist/search_24.svg?raw";
// import { DateSelector } from "@blaw/blaw-ui-shared-app-components"
export default {
  props: {
    disabledDates: {
      type: Object,
      default: () => ({})
    },
    highlighted: {
      type: Object,
      default: () => ({})
    },
    placeholder: {
      type: String,
      default: 'Select a Date'
    },
    tableData: {
      type: Array,
      required: true,
    },
    date: {
      type: Object,
      default: () => {
        return { from: null, to: null, preset: "date_range" }
      },
    },
  },
  components: {
    // TextInput,
    // Pill,
    // DateSelector,
  },
  data() {
    return {
      uniqueTypes: [],
      uniquetags: [],
      rangeTypeOptions: [
        { label: "Any", value: "no_date" },
        { label: "Single date", value: "single_date" },
        { label: "Date range", value: "date_range" },
        { label: "Last 7 days", value: "last_7_days" },
        { label: "Last 30 days", value: "last_30_days" },
      ],
      selectedDates: { start: null, end: null },
      filterCriteria: {
        localSearchTerm: '',
        dateRange: { from: null, to: null, preset: "date_range" },
        show: '',
        type: [],
        tags: [],
      },

      localSearchTerm: "",
      search24,
    };
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    updateDateRange(newValue) {
      this.dateRange = newValue;
    },
    emitFilter() {
      this.$emit('filter-changed', this.filterCriteria);
      console.log("=====", this.filterCriteria);
    },
    async fetchData() {
      try {
        this.loaded = false
        const response = {
          data: {
            tags: [
              "testtag",
              "new tag",
              "aa b",
              "zzzzzz",
              "abde",
              "thistag",
              "heytag",
              "aaa bbb",
              "aa dd",
              "a_aab",
              "a bde",
              "Tag",
              "tag_creation",
              "this tag",
              "a",
              "aa bb",
              "heythere",
              "a a",
              "a_aaaa",
              "z zz",
              "zzz",
              "newtag2",
              "part two",
              "a_aaa",
              "tagcreate",
              "a_aa",
              "a_ab",
              "dsafdsa",
              "abcd",
              "newtag",
              "hellotag",
              "test3",
              "bb",
              "aa cc",
              "z z",
              "basictag",
              "tag-creation"
            ],
            contentTypes: [
              "DOCKET",
              "GENERIC_LAW"
            ]
          }


        }

        this.uniquetags = response.data.tags
        this.uniqueTypes = response.data.contentTypes
      } catch (error) { /* empty */ }
    },
  },
};
</script>

<style scoped lang="scss">
.date-selector {
  margin-top: 10px;
}

.SearchResultsFilters {
  background-color: #fff;
  box-shadow: 0 2px 5px 0 rgba(42, 45, 48, 0.3);
  border: 1px solid #bbbbbb;
  border-radius: 2px;
  display: inline-block;
  padding: 8px;
  width: auto;

  .owner-label {
    border-bottom-color: rgba(62, 85, 58, 0.3);
    border-bottom-style: solid;
    color: #777c7f;
    padding: 6px 0px 6px 0px;
    margin: 0px 10px 5px 10px;
  }

  .filter {
    color: #2a2d30;
    font-family: "Open Sans";
    font-size: 20px;
    font-weight: bold;
    margin: 10px 0px 10px 10px;
  }
}

.date {
  display: flex;
  gap: 5;
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
