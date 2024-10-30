<template>
    <div>
      <input
        class="search-input"
        :placeholder="placeholder"
        v-model="localSearchTerm"
        @keyup.enter.stop="emitSearchTerm"
      />
      <img v-if="icon" :src="icon" alt="search-icon" class="search-icon" />
    </div>
  </template>
  
  <script>
  export default {
    name: 'SearchInput',
    props: {
      placeholder: {
        type: String,
        default: 'Search Keyword',
      },
      icon: {
        type: String,
        default: null,
      },
    },
    data() {
      return {
        localSearchTerm: '',
      };
    },
    methods: {
      emitSearchTerm() {
        this.$emit('search', this.localSearchTerm);
      },
    },
  };
  </script>
  
  <style scoped>
  .search-input {
    /* Styling for the input field */
    padding: 8px;
    width: 100%;
  }
  .search-icon {
    /* Styling for the search icon */
    width: 24px;
    height: 24px;
    position: absolute;
    right: 10px;
    top: 50%;
    transform: translateY(-50%);
  }
  </style>
  