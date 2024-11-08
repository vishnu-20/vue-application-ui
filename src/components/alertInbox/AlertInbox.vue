<template>
  <div class="flex-start">
    <div class="title">Alert Inbox</div>

    <!-- DatePicker Component with two-way data binding -->
    <DatePicker v-model="selectedDates" :range="true" />
    
    <!-- Display the selected start and end dates -->
    <p>Selected Start Date: {{ selectedDates.start }}</p>
    <p>Selected End Date: {{ selectedDates.end }}</p>
    
    <!-- Loading indicator -->
    <div v-if="isLoading" class="loading">
      loading ...........
    </div>

    <!-- Main content area with DataTable and TableFilter components -->
    <div class="flex" :class="isLoading ? 'hideData' : 'showData'">
      <div class="filter-component">
        <TableFilter :tableData="[...metadata]" @filter-changed="applyFilter" />
      </div>
    
      <div>
        <DataTable
          ref="dataTable"
          :metadata="metadata"
          :url="url"
          :selectable="true"
          @selectionChanged="updateSelectedRows"
          @updateError="updateError"
          @updateOwners="updateOwners"
          @updateIsAdmin="updateIsAdmin"
          @editAlert="handleEditAlert"
          @updateLoader="updateLoader"
        />
      </div>
    </div>
  </div>
</template>

<script>
import DataTable from '../DataTable.vue';
import DatePicker from './DatePicker.vue';
import TableFilter from './TableFilter.vue';

export default {
  name: "AlertInbox",
  props: {
    id: {
      type: String,
      required: true,
    },
  },
  components: {
    DataTable,
    TableFilter,
    DatePicker,
  },
  data() {
    return {
      selectedDates: { start: null, end: null }, // Selected dates from DatePicker
      data: null,
      selectedRows: [],
      error: false,
      resultCount: 0,
      pageNumber: 1,
      totalPages: 0,
      searchTerm: "",
      owners: [],
      isLoading: true,
      isAdmin: false,
      terms: [],
      subId: null,
      productId: "blaw",
      opened: false,
      url: `/alerts/management/results/e06c736669e2a1f685f780cf9eaa3d6b`,
      metadata: [
        { id: 1, fieldName: "checkbox", fieldText: "", width: 20, color: "#267ABD", checkBox: true },
        { id: 2, fieldName: "title", fieldText: "TITLE", width: 295, color: "#267ABD" },
        { id: 3, fieldName: "type", fieldText: "TYPE", width: 105, color: "#267ABD" },
        { id: 4, fieldName: "date", fieldText: "DATE", width: 105, color: "#267ABD" },
        { id: 5, fieldName: "clientMatter", fieldText: "CLIENT MATTER", width: 105, color: "#267ABD" },
      ],
    };
  },
  methods: {
    updateSelectedRows(selectedRows) {
      console.log("Selected Rows:", selectedRows);
      this.selectedRows = selectedRows;
    },
    applyFilter() {
      // Method to handle filtering logic
    },
    updateOwners(newOwners) {
      this.owners = newOwners;
    },
    updateIsAdmin(newValue) {
      this.isAdmin = newValue;
    },
    updateLoader(loaded) {
      this.isLoading = loaded;
    },
    handleEditAlert(detail) {
      this.subId = detail.subId;
      this.$refs.alertModalApp.showAlertModal({ detail: this.subId });
    },
    updateError(error) {
      this.error = error;
    },
  },
  mounted() {
    this.$nextTick(() => {
      if (this.$refs.dataTable) {
        this.$refs.dataTable.fetchData(this.pageNumber);
      }
    });
  },
};
</script>

<style scoped lang="scss">
.flex-start {
  width: 1450px;
  align-item: start;
  flex-direction: row;
  font-family: var(--font-family, "Open Sans", sans-serif);
  font-size: var(--font-size, 15px);
}
.title {
  font-size: 1.5rem;
  font-weight: 700;
  color: #2a2d30;
  margin-bottom: 2rem;
  padding-right: 50px;
}
.flex {
  display: flex;
  flex-direction: row;
}
.showData {
  display: flex;
}
.hideData {
  display: none;
}
.spinner {
  margin: auto;
}
.loading {
  margin: auto;
}
</style>
