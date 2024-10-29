<template>
  
  <div v-if="selectedItems.length > 0" class="table-actions-wrapper">
      <TableActions
        @mark-as-read="handleMarkAsRead"
        @mark-as-unread="handleMarkAsUnread"
        @delete-items="handleDeleteItems"
      />
    </div>
  <div class="main-container">
    <TableFilter :tableData="[...originalTableData]" @filter-changed="applyFilter" />


    <!-- Data Table Section -->
    <div class="table-section">
      <div class="table-container">
        <table class="data-table">
          <thead>
            <tr class="table-header">
              <th v-for="header in metadata" :key="header.fieldName"
                  :style="{ width: header.width + 'px', color: header.color }"
                  @click="sortColumn(header.fieldName)"
                  class="sortable">
                <div class="wrapper">
                  <input v-if="header.checkBox" type="checkbox" v-model="selectAll" 
                         @change="toggleSelectAll" @click.stop />
                  <span class="title">{{ header.fieldText }}</span>
                  <span v-if="sortedBy === header.fieldName" class="sort-icon">
                    <i v-if="sortDirection === 'asc'" class="arrow up"></i>
                    <i v-else class="arrow down"></i>
                  </span>
                </div>
              </th>
            </tr>
          </thead>
          <tbody>
            <tr class="table-data" v-for="item in filteredTableData" :key="item.id">
              <td>
                <input class="table-checkbox" type="checkbox" :checked="selectedItems.includes(item.id)"
                       @change="toggleItemSelection(item.id)" />
                {{ item.title }}
              </td>
              <td>{{ item.type }}</td>
              <td>{{ item.date }}</td>
              <td>{{ item.clientMatter }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import TableActions from './TableActions.vue';
import TableFilter from './TableFilter.vue';

export default {
  components: {
    TableFilter,
    TableActions,
  },
  data() {
    return {
      originalTableData: [
        { id: 1, title: "Sample Title 1", type: "Legal Search", date: "10/28/2024 11:02 AM", clientMatter: "None" },
        { id: 2, title: "Sample Title 2", type: "Legal Search", date: "10/28/2024 10:28 AM", clientMatter: "None" },
        { id: 3, title: "Sample Title 3", type: "Docket Search", date: "10/27/2024 10:28 AM", clientMatter: "Basic Tag" },
      ],
      filteredTableData: [],
      selectedItems: [],
      selectAll: false,
      sortedBy: null,
      sortDirection: 'asc',
      metadata: [
        { fieldName: "title", fieldText: "TITLE", width: 295, color: "#267ABD", checkBox: true },
        { fieldName: "type", fieldText: "TYPE", width: 105, color: "#267ABD" },
        { fieldName: "date", fieldText: "DATE", width: 105, color: "#267ABD" },
        { fieldName: "clientMatter", fieldText: "CLIENT MATTER", width: 105, color: "#267ABD" },
      ],
    };
  },
  methods: {
    fetchTableData() {
      this.filteredTableData = [...this.originalTableData];
    },
    toggleItemSelection(id) {
      if (this.selectedItems.includes(id)) {
        this.selectedItems = this.selectedItems.filter(itemId => itemId !== id);
      } else {
        this.selectedItems.push(id);
      }
      this.selectAll = this.selectedItems.length === this.originalTableData.length;
    },
    toggleSelectAll() {
      if (this.selectAll) {
        this.selectedItems = this.originalTableData.map(item => item.id);
      } else {
        this.selectedItems = [];
      }
    },
    applyFilter(criteria) {
      this.filteredTableData = this.originalTableData.filter(item => {
        return (
          (!criteria.keywords || item.title.toLowerCase().includes(criteria.keywords.toLowerCase())) &&
          (!criteria.show || item.type === criteria.show) &&
          (!criteria.dateRange || item.date === criteria.dateRange) &&
          (!criteria.type.length || criteria.type.includes(item.type)) &&
          (!criteria.tags.length || criteria.tags.includes(item.clientMatter))
        );
      });

      if (this.sortedBy) {
        this.sortColumn(this.sortedBy, true);
      }
    },
    sortColumn(fieldName, keepDirection = false) {
      if (this.sortedBy === fieldName && !keepDirection) {
        this.sortDirection = this.sortDirection === 'asc' ? 'desc' : 'asc';
      } else {
        this.sortedBy = fieldName;
        this.sortDirection = 'asc';
      }

      this.filteredTableData.sort((a, b) => {
        if (a[fieldName] < b[fieldName]) return this.sortDirection === 'asc' ? -1 : 1;
        if (a[fieldName] > b[fieldName]) return this.sortDirection === 'asc' ? 1 : -1;
        return 0;
      });
    },
    handleMarkAsRead() {
      // Get full details of selected items
      const selectedDetails = this.originalTableData.filter(item => this.selectedItems.includes(item.id));
      console.log('Marking selected items as read:', selectedDetails);
    },
    handleMarkAsUnread() {
      const selectedDetails = this.originalTableData.filter(item => this.selectedItems.includes(item.id));
      console.log('Marking selected items as unread:', selectedDetails);
    },
    handleDeleteItems() {
      const selectedDetails = this.originalTableData.filter(item => this.selectedItems.includes(item.id));
      console.log('Deleting selected items:', selectedDetails);
    },
}
,
  created() {
    this.fetchTableData();
  },
};
</script>

<style scoped lang="scss">
.main-container {
  display: flex;
  gap: 20px;
  padding: 20px;
}

.table-section {
  flex: 1;
}

.table-actions-wrapper {
  display: flex;
  justify-content: flex-end;
  margin-bottom: 10px;
}

.data-table {
  width: 100%;
  table-layout: fixed;
  box-sizing: border-box;
  border-radius: 2px;
  background-color: #ffffff;
  box-shadow: 0 2px 5px 0 rgba(0, 0, 0, 0.3);
}

.sortable {
  cursor: pointer;
}

.sort-icon {
  margin-left: 8px;
}

.arrow {
  border: solid #267ABD;
  border-width: 0 2px 2px 0;
  display: inline-block;
  padding: 4px;
  margin-left: 5px;
}

.arrow.up {
  transform: rotate(-135deg);
  -webkit-transform: rotate(-135deg);
}

.arrow.down {
  transform: rotate(45deg);
  -webkit-transform: rotate(45deg);
}

.table-data {
  height: 100px;
}

table,
th,
td {
  border: 1px solid #c5cacd;
  border-collapse: collapse;
}

.title {
  color: #777C7F;
  font-family: "Open Sans";
  font-size: 14px;
  font-weight: bold;
  letter-spacing: -0.36px;
  line-height: 32px;
  padding-right: 8px;
}

.table-checkbox {
  margin-right: 10px;
}

th {
  padding: 15px 12px 15px;
  line-height: 16px;
  color: #777c7f;
  font-family: "Open Sans";
  font-size: 14px;
  font-weight: bold;
  letter-spacing: 0.1px;
  background-color: #edf2f5;
}

.wrapper {
  display: flex;
}

td {
  padding-left: 12px;
  padding-right: 12px;
  color: #267abd;
  font-family: "Open Sans";
  font-size: 14px;
  font-weight: normal;
  letter-spacing: 0.1px;
}

td.odd {
  background-color: #f2f2f2;
}
</style>
