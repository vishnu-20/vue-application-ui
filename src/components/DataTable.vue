<template>
    <div>
        <table class="data-table">
            <!-- Header Row -->
            <tr class="table-header">
                <TableHeaderCell
                    v-for="(header, index) in metadata"
                    :key="header.fieldName"
                    :field-name="header.fieldName"
                    :header-text="header.fieldText"
                    :width="header.width"
                    :sortable="header.sortable"
                    :sort-field="sortField"
                    :sort-direction="sortDirection"
                    :selectable="selectable && index === 0"
                    :allSelected="allSelected"
                    :isFirstColumn="index === 0"
                    @toggleAllSelection="toggleAllSelection"
                    @sort="_sort"
                />
            </tr>

            <!-- Data Rows -->
            <tr class="table-data" v-for="(data, index) in tableData" :key="data.id">
                <TableCell
                    v-for="(header, idx) in metadata"
                    :key="header.fieldName"
                    :field-value="_cellValue(data, header)"
                    :link="_link(data, header)"
                    :icon-link="_iconLink(data, header)"
                    :index="index"
                    :selectable="selectable && idx === 0"
                    :isFirstColumn="idx === 0"
                    :isSelected="selectedRows.includes(data.id)"
                    @toggleRowSelection="toggleRowSelection"
                    @editAlert="handleEditAlertModal(data)"
                />
            </tr>
        </table>

        <!-- No data message -->
        <div v-if="!tableData.length" class="no-alerts">
            <h2>No alert subscriptions found</h2>
        </div>
    </div>
</template>

<script>
import TableCell from './TableCell.vue';
import TableHeaderCell from './TableHeaderCell.vue';



export default {
    name: "DataTable",
    emits: [
        "updateOwners",
        "updateIsAdmin",
        "editAlert",
        "updateLoader",
        "pageChange",
        "updateError",
    ],
    components: {
        TableHeaderCell,
        TableCell,

    },
    props: {
        url: {
            type: String,
            required: true,
        },
        metadata: {
            type: Array,
            default: () => [],
        },
        selectable: {
            type: Boolean,
            default: false,
        },
    },
    data() {
        return {
            selectedRows: [],
            allSelected: false,
            count: 0,
            sortField: "",
            sortDirection: "",
            currentPage: 1,
            pageSize: 25,
            tableData: [],
            loaded: false,
            error: false,
            isAdmin: false,
            owners: [],
            // DataTable emits events for changes to these params
            managedQueryParams: [
                "page_number",
                "search_term",
                "field",
                "order",
                "pageSize",
            ],
        }
    },
    computed: {
        showTable() {
            return this.loaded && !this.error && this.count > 0
        },
        showError() {
            return this.loaded && this.error
        },
    },
    methods: {

            toggleAllSelection() {
            // If allSelected, clear; otherwise, select all rows
            this.allSelected = !this.allSelected;
            this.selectedRows = this.allSelected ? this.tableData.map(row => row.id) : [];
            this.$emit("selectionChanged", this.selectedRows);  // Notify parent of selected rows
        },
        toggleRowSelection(rowId) {
            // If rowId in selectedRows, remove; otherwise, add it
            if (this.selectedRows.includes(rowId)) {
                this.selectedRows = this.selectedRows.filter(id => id !== rowId);
            } else {
                this.selectedRows.push(rowId);
            }
            // Update header checkbox status
            this.allSelected = this.selectedRows.length === this.tableData.length;
            this.$emit("selectionChanged", this.selectedRows);
  
        },
        handleEditAlertModal(rowData) {
            this.$emit("editAlert", rowData)
        }, async fetchData(pageNumber) {
            try {
                this.loaded = false
                // const response = await DataTableManager.fetch(this.url, {
                //   params: {
                //     search_term: this.$parent.searchTerm,
                //     page_number: pageNumber,
                //     users: this.users,
                //   },
                //   headers: { "Content-Type": "application/json" },
                // })
                const response = { pageNumber: pageNumber };
                this.tableData = [{ id: 1, title: "Sample Title 1", type: "Legal Search", date: "10/28/2024 11:02 AM", clientMatter: "None" },
                { id: 2, title: "Sample Title 2", type: "Legal Search", date: "10/28/2024 10:28 AM", clientMatter: "None" },
                { id: 3, title: "Sample Title 3", type: "Docket Search", date: "10/27/2024 10:28 AM", clientMatter: "Basic Tag" },]
                this.count = response.data.data.resultCount
                this.isAdmin = response.data.data.criteria.admin
                this.owners = response.data.owners
                if (this.isAdmin) {
                    this.$emit("updateIsAdmin", this.isAdmin)
                } if (this.owners) {
                    this.$emit("updateOwners", this.owners)
                }
                this.$emit("updateLoader", false)
            } catch (error) {
                this.error = true
                this.$emit("updateLoader", false)
                this.$emit("updateError", true)
            } finally {
                this.loaded = true
            }
        },
        _updateParams(event) {
            this.pageSize = event.pageSize ? event.pageSize : this.pageSize
            this.currentPage = event.pageNum
            this.fetchData(this.currentPage)
            this.$emit("pageChange", event)
        },
        _sort(data) {
            this.currentPage = 1
            this.sortField = data.sortField
            this.sortDirection = data.sortDirection
            this.$emit("sortEvent", {
                sortField: data.sortField,
                sortDirection: data.sortDirection,
            })
            this.fetchData()
        }, _cellValue(data, header) {
            const key = header.fieldName
            if (data[key] && data[key].value) {
                return data[key].value
            } else if (data[key] && typeof data[key] === "object") {
                return data[key]["id"]
            } else if (
                this.isAdmin &&
                data[header["_fieldName"]] &&
                data[header["_fieldName"]].length > 0
            ) {
                return data[key] + "<hr>" + data[header["_fieldName"]].join(", <br>")
            } else if (!this.isAdmin && data[header["_fieldName"]]) {
                return data[header["_fieldName"]].join(", <br>")
            } else if (header.hyperLink) {
                return key
            }
            return data[key]
        },
        _link(data, header) {
            if (header.hyperLink) {
                if (header.fieldName === "Edit Alert") {
                    return "#"
                } else {
                    return data[header.fieldName].link || this._cellValue(data, header)
                }
            }
            return ""
        }, _iconLink(data, header) {
            if (header.iconHyperLink) {
                return data[header.fieldName].iconLink
            }
            return ""
        },
        _tableWidth() {
            const reducer = (accumulator, currentValue) => accumulator + currentValue
            return this.metadata.map((m) => m.width).reduce(reducer)
        },
    },
}
</script>
<style scoped lang="scss">
.data-table {
    width: 100%;
    table-layout: fixed;
    box-sizing: border-box;
    border-radius: 2px;
    background-color: #ffffff;
    box-shadow: 0 2px 5px 0 rgba(0, 0, 0, 0.3);

    .table-data {
        height: 44px;
    }
}

table,
th,
td {
    border: 1px solid #c5cacd;
    border-collapse: collapse;
}

.header {
    display: flex;
    align-items: center;
    justify-content: flex-start;
    margin-bottom: 8px;

    .title {
        height: 32px;
        color: #292e31;
        font-family: "Open Sans";
        font-size: 20px;
        font-weight: 600;
        letter-spacing: -0.36px;
        line-height: 32px;
        padding-right: 8px;
    }
}

.navigation {
    display: flex;
    flex-grow: 1;
    padding-top: 10px;
    padding-bottom: 8px;
    max-width: 300px;
}

.count {
    font-family: "Open Sans";
    padding-top: 5px;
    padding-left: 5px;
}

a {
    text-decoration: None;
}

.editAnchor {
    display: flex;
    padding: 10px 12px 10px 12px;
    width: 100px;
}

.no-alerts {
    border-top: 1px solid #c1c5cb;
    box-shadow: 0 2px 5px 0 rgba(42, 45, 48, 0.3);
    border-radius: 2px;
    background-color: #ffffff;
    height: 65px;
    width: 1000px;
    text-align: center;
}
</style>