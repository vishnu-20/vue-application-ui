<template>
    <th
      class="col-header"
      :class="{ 'sort-column': isSortField }"
      :name="fieldName"
      :style="{ width: width - 14 + 'px' }"
    >
      <div class="wrapper">
        <span v-if="selectable && isFirstColumn">
          <input
            type="checkbox"
            :checked="allSelected"
            @change="$emit('toggleAllSelection')"
          />
        </span>
        <span class="text">{{ headerText }}</span>
        <span class="sort-icons" v-if="sortable">
          <svg
            v-if="!isSortAsc"
            @click="_sort('ASCENDING')"
            class="sort-asc"
            :fill="caretColor"
            v-html="caretUp24"
            width="16"
            height="16"
            :class="{ 'show-single': isSortDesc }"
          ></svg>
          <svg
            v-if="!isSortDesc"
            @click="_sort('DESCENDING')"
            class="sort-desc"
            :fill="caretColor"
            v-html="caretDown24"
            width="16"
            height="16"
            :class="{ 'show-single': isSortAsc }"
          ></svg>
        </span>
      </div>
    </th>
  </template>
  
<script>
import caretUp24 from "@fishtank/icons/dist/caret-up_24.svg?raw"
import caretDown24 from "@fishtank/icons/dist/caret-down_24.svg?raw"
export default {
  name: "TableHeaderCell",
  props: {
    fieldName: { type: String, required: true },
    headerText: { type: String, required: true },
    sortable: { type: Boolean, default: true },
    sortField: { type: String, required: true },
    sortDirection: { type: String, default: "None" },
    width: Number,
    selectable: { type: Boolean, default: false },
    isFirstColumn: { type: Boolean, default: false },
    allSelected: Boolean,
  },
  components: {},
  computed: {
    isSortField() {
      return this.fieldName === this.sortField
    },
    isSortAsc() {
      if (this.fieldName !== this.sortField) {
        return false
      }
      return this.sortDirection === "ASCENDING"
    },
    isSortDesc() {
      if (this.fieldName !== this.sortField) {
        return false
      }
      return this.sortDirection === "DESCENDING"
    },caretColor() {
      if (this.isSortDesc || this.isSortAsc) {
        return "#ffffff"
      } else {
        return "#777C7F"
      }
    },
  },
  methods: {
    _sort(sortDirection) {
      this.$emit("sort", {
        sortField: this.fieldName,
        sortDirection: sortDirection,
      })
    },
  },
  data() {
    return {
      caretUp24,
      caretDown24,
    }
  },
}
</script>
<style scoped lang="scss">
th {
  padding: 15px 12px 15px;
  line-height: 16px;
  color: #777c7f;
  font-family: "Open Sans";
  font-size: 14px;
  font-weight: bold;
  letter-spacing: 0.1px;
  background-color: #edf2f5;
  .FishtankIcon {
    cursor: pointer;
  }
  .show-single {
    padding-top: 10px;
  }
}
th .text {
  padding-left: 12px;
}th.col-header.sort-column {
  color: #ffffff;
  background-color: #505558;
}
.wrapper {
  display: flex;
  justify-content: space-between;
}
.sort-icons .sort-asc,
.sort-icons .sort-desc {
  line-height: 16px;
}
</style>