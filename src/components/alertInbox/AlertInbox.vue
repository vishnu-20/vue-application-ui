<template>
  <div class="flex-start">
    <div class="alert-inbox-titile-container">
      <div class="title">Alert Inbox</div>
      <TableActions />
    </div>
    <div class="flex">
      <div class="filter-component">
        <TableFilter :tableData="[...metadata]" @filter-changed="applyFilter" />
      </div>
      <div>
        <DataTable ref="dataTable" :queryParams="queryParams" :metadata="metadata" :url="url" @updateError="updateError"
          @editAlert="handleEditAlert" />
      </div>
    </div>
  </div>
</template>
<script>
import TableFilter from "./TableFilter.vue";
import TableActions from "./TableActions.vue";
import DataTable from "../DataTable.vue";
export default {
  name: "AlertInbox",
  props: {
  },
  components: {
    DataTable,
    TableFilter,
    TableActions,
  },
  data() {
    return {
      queryParams: {
        searchTerm: "",
        type: ["DOCKET_TRACK"],
        sort_field: "title",
        sort_direction: "asc",
        page_number: 1,
        users: [],
        courts: [],
      },
      data: null,
      dateRange: { from: null, to: null, preset: "date_range" },
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
      url: '/alerts/inbox/findById/6673555',
      metadata: [
        {
          fieldName: "name", fieldText: "TITLE", width: 295, color: "#267ABD", hyperLink: true,
          link: "/product/blaw/document/{docId}",
        },
        { fieldName: "contentType", fieldText: "TYPE", width: 105, color: "#267ABD" },
        {
          fieldName: "created", fieldText: "DATE", width: 105, color: "#267ABD", date: true,
          format: "MM/DD/YYYY hh:mm A", sortable: true
        },
        { fieldName: "clientMatter", fieldText: "CLIENT MATTER", width: 105, color: "#267ABD" },
      ],
    }
  },
  methods: {

    handleSearchTermUpdate(term) {
      if (term) {
        this.terms = [{ label: term, hideRemove: false }]
      } else {
        this.terms = []
      }
      this.searchTerm = term
      this.$refs.dataTable.fetchData(this.pageNumber)
    },
    updateOwners(newOwners) {
      this.owners = newOwners
    },
    updateIsAdmin(newValue) {
      this.isAdmin = newValue
    },
    updateLoader(loaded) {
      this.isLoading = loaded
    },
    removeTerm(index) {
      this.terms.splice(index, 1)
      if (this.terms.length === 0) {
        this.searchTerm = ""
      }
      this.$refs.dataTable.fetchData(this.pageNumber)
    },
    handleOwnerCheckboxChange(selectedOwners) {
      this.$refs.dataTable.users = selectedOwners
      this.$refs.dataTable.fetchData(this.pageNumber)
    },
    handleEditAlert(detail) {
      this.subId = detail.subId
      this.$refs.alertModalApp.showAlertModal({
        detail: this.subId,
      })
    },
    updateError(error) {
      this.error = error
    },
  },
  mounted() {
    this.$nextTick(() => {
      if (this.$refs.dataTable) {
        this.$refs.dataTable.fetchData(this.pageNumber)
      }
    })
  },
}
</script>
<style scoped lang="scss">
.alert-inbox-titile-container {
  display: flex;
  justify-content: space-between;
}

.flex-start {
  width: 1450px;
  align-item: start;
  flex-direction: row;

  .SearchResultsFilters {
    margin-right: 16px;
  }

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

.DateSelector .Dropdown {
  display: none;
  visibility: hidden;
  pointer-events: none;
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
