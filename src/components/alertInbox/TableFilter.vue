<template>
  <div class="SearchResultsFilters" v-if="!this.error">
    <h2 class="filter">Filters</h2>
    <div class="owner-label">Search with Keyword</div>
    <TextInput
      class="search-input"
      @keyup.enter.stop="emitSearchTerm"
      v-model="localSearchTerm"
      placeholder="Search Keyword"
      :icon="search24"
    />
 
  </div>
</template>
<script>
import search24 from "@fishtank/icons/dist/search_24.svg?raw"
import TextInput from "../shared/TextInput.vue";

export default {
  name: "FiltersComponent",
  components: {
    TextInput,
  },
  props: {
      searchTerm: String,
      terms: Array,
      owners: Array,
      isAdmin: Boolean,
      removeTerm: Function,
      error: Boolean,
    },
    data() {
      return {
        localSearchTerm: this.searchTerm,
        localSearchQuery: "",
        selectedOwners: [],
        search24,
      }
    },
    computed: {
      filterOwners() {
        const query = this.localSearchQuery.toLowerCase()
        return this.owners.filter((owner) =>
          owner.metadata_label.toLowerCase().includes(query),
        )
      },
    },
    methods: {
      emitSearchTerm() {
        this.$emit("updateSearchTerm", this.localSearchTerm)
      },
      
    },
    watch: {
      searchTerm(newValue) {
        this.localSearchTerm = newValue
      },
    },
  }
  </script>
  <style scoped lang="scss">
  .SearchResultsFilters {
    background-color: #fff;
    box-shadow: 0 2px 5px 0 rgba(42, 45, 48, 0.3);
    border: 1px solid #bbbbbb;
    border-radius: 2px;
    display: inline-block;
    padding: 8px;
    width: auto;
    width: 400px;
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
    .search-input {
      margin: 0px 10px 5px 10px;
      border-radius: 2px;
    }
    .owner-list {
      margin: 0px 10px 5px 10px;
      padding-top: 10px;
    }
  }
  </style>