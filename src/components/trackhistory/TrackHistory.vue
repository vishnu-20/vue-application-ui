<!-- <AlertModalApp
          ref="alertModalApp"
          :isEliteEnabled="false"
          :saveButtonText="'save'"
          :reloadOnSave="true"
          v-bind="{
            opened,
            productId,
            subId,
          }"
        />
        <div>
          <DataTable
            ref="dataTable"
            :queryParams="queryParams"
            :metadata="metadata"
            :url="url"
            @updateError="updateError"
            @editAlert="handleEditAlert"
          />
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import FiltersComponent from "./FiltersComponent.vue"
import DataTable from "../DataTable.vue"
import { AlertModalApp, ErrorPage } from "@blaw/blaw-ui-shared-app-components"
export default {
  name: "TrackHistory",
  components: {
    AlertModalApp,
    FiltersComponent,
    DataTable,
    ErrorPage,
  },
  data() {
    return {
      error: false,
      pageNumber: 1,
      owners: [],
      terms: [],
      subId: null,
      productId: "blaw",
      opened: false,
      url: "/alert/mgmt/docket_track_history",
      queryParams: {
        searchTerm: "",
        type: ["DOCKET_TRACK"],
        sort_field: "title",
        sort_direction: "asc",
        page_number: 1,
        users: [],
        courts: [],
      },
metadata: [
        {
          fieldName: "title",
          fieldText: "TITLE",
          width: 295,
          color: "#267ABD",
        },
        {
          fieldName: "courtName",
          fieldText: "COURT",
          width: 165,
          color: "#267ABD",
          sortable: false,
        },
        {
          fieldName: "displayDocketNumber",
          fieldText: "DOCUMENT NUMBER",
          width: 165,
          color: "#267ABD",
          sortable: false,
          hyperLink: true,
          link: "/product/blaw/document/{docId}",
        },
{
          fieldName: "created",
          fieldText: "CREATED DATE",
          width: 165,
          color: "#267ABD",
          date: true,
          format: "MM/DD/YYYY hh:mm A",
        },
        {
          fieldName: "ownerFirstName",
          fieldText: "OWNER / RECIPIENTS",
          width: 165,
          color: "#267ABD",
          secondFieldName: "ownerLastName",
          thirdFieldName: "recipients",
          sortable: false,
        },
        {
          fieldName: "Edit Alert",
          fieldText: "EDIT",
          width: 120,
          color: "#267ABD",
          sortable: false,
          icon: "edit24",
          eventName: "click",
        },
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
      this.queryParams["searchTerm"] = term
      this.queryParams["page_number"] = 1
      this.$refs.dataTable.fetchData()
    },
    removeTerm(index) {
      this.terms.splice(index, 1)
      if (this.terms.length === 0) {
        this.queryParams["searchTerm"] = ""
      }
      this.queryParams["page_number"] = 1
      this.$refs.dataTable.fetchData()
    },
handleOwnerCheckboxChange(selectedOwners) {
      this.queryParams["users"] = selectedOwners
      this.queryParams["page_number"] = 1
      this.$refs.dataTable.fetchData()
    },
    handleCourtCheckboxChange(selectedCourts) {
      this.queryParams["courts"] = selectedCourts
      this.queryParams["page_number"] = 1
      this.$refs.dataTable.fetchData()
    },
    handleEditAlert(detail) {
      this.subId = detail.ssid.toString()
      this.$refs.alertModalApp.showAlertModal({
        detail: this.subId,
      })
    },
    updateError(error) {
      this.error = error
    },
  },
mounted() {
    Promise.all([
      this.$refs.filtersComponent.fetchData(),
      this.$refs.dataTable.fetchData(),
    ]).then(function () {})
  },
}
</script>
<style scoped lang="scss">
.track-history-container {
  width: 1480px;
  font-family: var(--font-family, "Open Sans", sans-serif);
  font-size: var(--font-size, 15px);
}
.title {
  font-size: 1.5rem;
  font-weight: 700;
  color: #2a2d30;
  margin-bottom: 2rem;
}
.flex {
  display: flex;
  flex-direction: row;
}
.filter-component {
  padding-right: 16px;
}
</style> -->
