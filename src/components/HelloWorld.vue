<template>
  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th><input type="checkbox" v-model="selectAll" @change="toggleSelectAll" /></th>
          <th>TITLE</th>
          <th>TYPE</th>
          <th>DATE</th>
          <th>CLIENT MATTER</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in tableData" :key="item.id">
          <td><input type="checkbox" v-model="selectedItems" :value="item.id" /></td>
          <td>{{ item.title }}</td>
          <td>{{ item.type }}</td>
          <td>{{ item.date }}</td>
          <td>{{ item.clientMatter }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: [{
        title : "title",
        type : "type",
        name : "name",
        clientMatter : "clientMatter"

      }],
      selectedItems: [],
      selectAll: false,
    };
  },
  methods: {
    async fetchTableData() {
      console.log("-----------");
      
    },
    toggleSelectAll() {
      if (this.selectAll) {
        this.selectedItems = this.tableData.map(item => item.id);
      } else {
        this.selectedItems = [];
      }
    },
  },
  created() {
    this.fetchTableData();
  },
};
</script>

<style scoped>
.table-container {
  width: 100%;
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  padding: 10px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

th {
  background-color: #f9f9f9;
}

input[type="checkbox"] {
  cursor: pointer;
}
</style>
