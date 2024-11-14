<template>
    <div class="data-table-container">
        <table class="data-table" v-if="true">
            <tr class="table-header">
                <TableHeaderCell v-for="(header) in metadata" :key="header.fieldName" :field-name="header.fieldName"
                    :header-text="header.fieldText" :width="header.width" :sortable="header.sortable"

                    :sort-field="this.queryParams['sort_field']" 
                    :sort-direction="this.queryParams['sort_direction']"
                    @sort="_sort($event)" />
            </tr>
            <tr class="table-data" v-for="(data, index) in tableData" :key="data.alertId">
                <TableCell v-for="(header) in metadata" :key="header.fieldName" 
                :field-value="_cellValue(data, header)"
                    :field-icon="_fieldIcon(data, header)" :link="_link(data, header)"
                    :icon-link="_iconLink(data, header)" :index="index" :event="header.eventName"
                    @editAlert="handleEditAlertModal(data)" />
            </tr>
        </table>
        <div v-if="!this.loaded" class="loading">
            <Spinner />
        </div>
        <div v-if="!showTable && this.loaded && !this.error" class="no-alerts">
            <h2>No alert subscriptions found</h2>
        </div>
        <span class="navigation" v-if="showTable">

        </span>
    </div>
</template>
<script>
import TableHeaderCell from "./TableHeaderCell.vue"
import TableCell from "./TableCell.vue"
import dayjs from "dayjs"
export default {
    name: "DataTable",
    emits: ["editAlert", "updateError"],
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
        queryParams: {
            type: Object,
            default: () => { },
        },

    },
    data() {
        return {
            count: 0,
            pageSize: 25,
            tableData: [],
            loaded: false,
            error: false,
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
        handleEditAlertModal(rowData) {
            this.$emit("editAlert", rowData)
        },
        async fetchData() {
            try {
                this.loaded = false
                this.error = false
                const response = {

                    results: [
                        {
                            "alertId": 4702695,
                            "ssid": 100056035,
                            "uuid": 6673555,
                            "contentType": "GENERIC_LAW",
                            "name": "Suit test search",
                            "created": "2021-08-16T12:51:37.000+00:00",
                            "lastUpdated": "2021-08-16T12:51:38.000+00:00",
                            "reportId": 3955845,
                            "status": "ACTIVE",
                            "userStatus": "UNREAD",
                            "clientMatter": null,
                            "origin": "BLAW",
                            "reportStatus": "FULL_REPORT",
                            "tags": [
                                "testtag"
                            ]
                        },
                        {
                            "alertId": 4702697,
                            "ssid": 100056037,
                            "uuid": 6673555,
                            "contentType": "GENERIC_LAW",
                            "name": "Suit test search",
                            "created": "2021-08-16T12:51:37.000+00:00",
                            "lastUpdated": "2021-08-16T12:51:38.000+00:00",
                            "reportId": 3955847,
                            "status": "ACTIVE",
                            "userStatus": "UNREAD",
                            "clientMatter": null,
                            "origin": "BLAW",
                            "reportStatus": "FULL_REPORT",
                            "tags": [
                                "testtag"
                            ]
                        },
                        {
                            "alertId": 4702707,
                            "ssid": 100056035,
                            "uuid": 6673555,
                            "contentType": "GENERIC_LAW",
                            "name": "Suit test search",
                            "created": "2021-08-16T12:51:37.000+00:00",
                            "lastUpdated": "2021-08-16T12:51:38.000+00:00",
                            "reportId": 3955855,
                            "status": "ACTIVE",
                            "userStatus": "UNREAD",
                            "clientMatter": null,
                            "origin": "BLAW",
                            "reportStatus": "FULL_REPORT",
                            "tags": [
                                "testtag"
                            ]
                        },
                        {
                            "alertId": 4702709,
                            "ssid": 100056037,
                            "uuid": 6673555,
                            "contentType": "GENERIC_LAW",
                            "name": "Suit test search",
                            "created": "2021-08-16T12:51:37.000+00:00",
                            "lastUpdated": "2021-08-16T12:51:38.000+00:00",
                            "reportId": 3955853,
                            "status": "ACTIVE",
                            "userStatus": "UNREAD",
                            "clientMatter": null,
                            "origin": "BLAW",
                            "reportStatus": "FULL_REPORT",
                            "tags": [
                                "testtag"
                            ]
                        },
                    ]

                };
                if (response.results === null) {
                    this.error = true
                    this.tableData = []
                } else {
                    this.tableData = response.results
                }
                this.count = response.facets.count
                // eslint-disable-next-line vue/no-mutating-props
                this.queryParams["page_number"] = parseInt(response.data.page_number)
            } catch (error) {
                this.error = true
                this.$emit("updateError", true)
            } finally {
                this.loaded = true
            }
        },
        _updateParams(event) {
            this.pageSize = event.pageSize ? event.pageSize : this.pageSize
            // eslint-disable-next-line vue/no-mutating-props
            this.queryParams["page_number"] = event.pageNum
            this.fetchData()
        },
        _sort(data) {
            // eslint-disable-next-line vue/no-mutating-props
            this.queryParams["page_number"] = 1
            // eslint-disable-next-line vue/no-mutating-props
            this.queryParams["sort_field"] = data.sortField
            // eslint-disable-next-line vue/no-mutating-props
            this.queryParams["sort_direction"] = data.sortDirection
            this.fetchData()
        },
        _updateParams(event) {
            this.pageSize = event.pageSize ? event.pageSize : this.pageSize
            // eslint-disable-next-line vue/no-mutating-props
            this.queryParams["page_number"] = event.pageNum
            this.fetchData()
        },
        _sort(data) {
            // eslint-disable-next-line vue/no-mutating-props
            this.queryParams["page_number"] = 1
            // eslint-disable-next-line vue/no-mutating-props
            this.queryParams["sort_field"] = data.sortField
            // eslint-disable-next-line vue/no-mutating-props
            this.queryParams["sort_direction"] = data.sortDirection
            this.fetchData()
        },
        _cellValue(data, header) {
            const key = header.fieldName
            if (data[key] && data[key].value) {
                return data[key].value
            } else if (data[header.secondFieldName]) {
                let processedString = data[key] + " " + data[header.secondFieldName]
                const processedData = []
                if (
                    data[header.thirdFieldName] &&
                    data[header.thirdFieldName].length > 0
                ) {
                    data[header["thirdFieldName"]].forEach((recipient) => {
                        if (recipient["firstName"] && recipient["lastName"]) {
                            processedData.push(
                                recipient["firstName"] + " " + recipient["lastName"],
                            )
                        } else {
                            processedData.push(recipient["email"])
                        }
                    })
                    processedString += "<hr>" + processedData.join(", <br>")
                }
                return processedString
            } else if (header.date) {
                if (typeof data[key] === "string") {
                    data[key] = data[key].replace(/-/g, "/").substring(0, 20)
                }
                return this.formatDate(data[key], header.format)
            } else if (header.eventName) {
                return key
            }
            return data[key]
        },
        formatDate(date, format) {
            if (format) {
                return dayjs(date).format(format)
            } else {
                return dayjs(date).format("MM/DD/YYYY hh:mm A")
            }
        },
        _link(data, header) {
            if (header.hyperLink) {
                if (header.fieldName === "Edit Alert") {
                    return "#"
                } else if (header.link) {
                    const parts = header.link.split(/[{}]/)
                    return `${parts[0]}${data[parts[1]]}`
                } else {
                    return data[header.fieldName].link || this._cellValue(data, header)
                }
            }
            return ""
        },
        _iconLink(data, header) {
            if (header.iconHyperLink) {
                return data[header.fieldName].iconLink
            }
        },
        _fieldIcon(data, header) {
            if (header.icon) {
                return header.icon
            }
            return ""
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

.spinner {
    margin: auto;
}

.loading {
    width: 1164px;
}

.data-table-container {
    height: 100%;
}
</style>