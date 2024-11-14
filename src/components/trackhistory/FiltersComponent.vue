<template>
    <div class="SearchResultsFilters">
      <h2 class="filter">Filters</h2>
      <div class="owner-label">Search with Keyword</div>
      <TextInput
        class="search-input"
        @keyup.enter.stop="emitSearchTerm"
        v-model="localSearchTerm"
        placeholder="Search Keyword"
        :icon="search24"
      />
      <Pill
        class="search-input"
        v-for="(pill, index) in terms"
        :disabled="pill.disabled"
        :hide-remove="pill.hideRemove"
        :key="index"
        :label="pill.label"
        @remove="removeTerm(index)"
      />
  <div v-if="!this.loaded" class="loading">
        <Spinner size="medium" />
      </div>
      <div v-else>
        <div class="owner-label">Courts</div>
        <TextInput
          :icon="search24"
          class="search-input owner"
          v-model="courtQuery"
          placeholder="Search court by name..."
        />
        <div
          class="court-list"
          style="max-height: 300px; overflow-y: scroll"
          v-if="courts"
        >
          <div
            v-for="(courtName, courtCode) in filterCourts"
            :key="courtCode"
            class="court-item"
          >
  <input
              type="checkbox"
              :value="courtCode"
              v-model="selectedCourts"
              @change="emitCourtSelection()"
            />
            <label class="text-ellipse" :title="courtName">
              {{ courtName }}
            </label>
          </div>
        </div>
        <div v-if="isAdmin">
          <div class="owner-label">Owner</div>
          <TextInput
            :icon="search24"
            class="search-input owner"
            v-model="localSearchQuery"
            placeholder="Search owner by name..."
          />
          <div
            class="owner-list"
            style="max-height: 300px; overflow-y: scroll"
            v-if="owners"
          >
  <div
              v-for="owner in filterOwners"
              :key="owner.uuid"
              class="owner-item"
            >
              <label :style="owner.style">
                <input
                  type="checkbox"
                  :value="owner.uuid"
                  v-model="selectedOwners"
                  @change="emitOwnerSelection()"
                />
                {{ owner.first_name }} {{ owner.last_name }}
              </label>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  <script>
  import { Spinner } from "@blaw/blaw-ui-generic-components"
  import { TextInput, Pill } from "@blaw/blaw-ui-generic-components"
  import search24 from "@fishtank/icons/dist/search_24.svg?raw"
  import DataTableManager from "../../modules/DataTableManager.js"
  export default {
    name: "FiltersComponent",
    components: {
      TextInput,
      Pill,
      Spinner,
    },
    props: {
      searchTerm: String,
      terms: Array,
      removeTerm: Function,
    },
    data() {
      return {
        localSearchTerm: this.searchTerm,
        localSearchQuery: "",
       courtQuery: "",
        selectedOwners: [],
        selectedCourts: [],
        search24,
        isAdmin: false,
        owners: [],
        courts: [],
        url: "/alert/mgmt/get_facets",
        loaded: false,
      }
    },
  computed: {
      filterOwners() {
        const query = this.localSearchQuery.toLowerCase()
        return this.owners
          .filter(
            (owner) =>
              owner.first_name.toLowerCase().includes(query) &&
              owner.last_name.toLowerCase().includes(query),
          )
          .sort((a, b) => {
            const firstNameA = a.first_name.toLowerCase()
            const firstNameB = b.first_name.toLowerCase()
            if (firstNameA === firstNameB) {
              return a.last_name
                .toLowerCase()
                .localeCompare(b.last_name.toLowerCase())
            }
            return firstNameA.localeCompare(firstNameB)
          })
      },
  filterCourts() {
        const query = this.courtQuery.toLowerCase()
        const filteredCourts = Object.entries(this.courts)
          .filter(([_key, value]) => value.toLowerCase().includes(query))
          .sort(([_keyA, valueA], [_keyB, valueB]) =>
            valueA.localeCompare(valueB.toLowerCase()),
          )
          .reduce((crt, [key, value]) => {
            crt[key] = value
            return crt
          }, {})
        return filteredCourts
      },
    },
    methods: {
      async fetchData() {
        try {
          const response = await DataTableManager.fetch(this.url, {
            params: {
              type: ["DOCKET_TRACK"],
            },
          })
  this.courts = response.data.courts
          this.owners = response.data.owners
          this.isAdmin = response.data.isAdmin
          this.$emit("updateError", false)
        } catch (error) {
          this.$emit("updateError", true)
        } finally {
          this.loaded = true
        }
      },
      emitSearchTerm() {
        this.$emit("updateSearchTerm", this.localSearchTerm)
      },
      emitCourtSelection() {
        this.$emit("updateSelectedCourts", this.selectedCourts)
      },
      emitOwnerSelection() {
        this.$emit("updateSelectedOwners", this.selectedOwners)
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
    width: 300px;
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
    .owner-list,
    .court-list {
      margin: 0px 10px 5px 10px;
      padding-top: 10px;
      background-color: #f2f2f2;
      border-radius: 5px;
    }
    .court-item {
      display: flex;
      align-items: baseline;
      padding-bottom: 4px;
    }
    .owner-item {
      padding-bottom: 4px;
    }
  text-ellipse {
      display: block;
      //text-transform: uppercase;
      width: 260px;
      padding-left: 4px;
    }
    .spinner {
      margin: auto;
    }
  }
  </style>
  