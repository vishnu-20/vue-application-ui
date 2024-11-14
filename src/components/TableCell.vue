<template>
  <td :class="{ odd: isOddRow }" :style="{ color: color }">
    <div :class="{ wrapper: true, centered: hasIcon }">
      <span v-if="hasIcon" class="cell-icon">
        <a v-if="hasIconLink" class="link" :href="iconLink">
          <svg v-html="getIcon"></svg>
        </a>
        <svg
          v-if="!hasIconLink"
          v-html="getIcon"
          :fill="iconColor"
          width="20"
          height="20"
        ></svg>
      </span>
      <span class="text-container">
        <span
          v-if="!hasLink && !isClickEvent"
          class="text"
          v-html="fieldValue"
        ></span>
        <a v-if="hasLink" class="link" :href="link" target="_blank">
          {{ fieldValue }}
        </a>
<a v-if="isClickEvent" class="link" @click="handleOpenModal">
          {{ fieldValue }}
        </a>
      </span>
    </div>
  </td>
</template>
<script>
import edit24 from "@fishtank/icons/dist/edit_24.svg?raw"
const iconMap = {
  edit24: edit24,
}
export default {
  name: "TableCell",
  components: {},
  props: {
    index: {
      type: Number,
    },
    event: {
      type: String,
    },
    fieldValue: {
      type: String,
      default: "",
    },
    link: {
      type: String,
      default: "",
    },
    iconLink: {
      type: String,
      default: "",
    },
    fieldIcon: {
      type: String,
      default: "",
    },
    width: {
      type: Number,
      default: 100,
    },
color: {
      type: String,
      default: "#292E31",
    },
    iconColor: {
      type: String,
      default: "#267ABD",
    },

  },
  computed: {
    isOddRow() {
      return this.index % 2 !== 0
    },
    hasIcon() {
      return this.fieldIcon !== ""
    },
    hasLink() {
      return this.link !== ""
    },
    hasIconLink() {
      return this.iconLink !== ""
    },
    getIcon() {
      return iconMap[this.fieldIcon] || ""
    },
    isClickEvent() {
      return this.event === "click"
    },
  },
  methods: {
    handleOpenModal(data) {
      this.$emit("editAlert", data)
    },
  },
}
</script>
<style scoped lang="scss">
td {
  padding-left: 12px;
  padding-right: 12px;
  color: #267abd;
  font-family: "Open Sans";
  font-size: 14px;
  font-weight: normal;
  letter-spacing: 0.1px;
}
td a {
  color: #267abd;
  text-decoration: none;
  cursor: pointer;
}
td.odd {
  background-color: #f2f2f2;
}
.wrapper {
  display: flex;
  justify-content: space-between;
  gap: 4px;
}
.link {
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  gap: 5px;
}
.text-container {
  margin-top: 10px;
  margin-bottom: 10px;
  overflow: hidden;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}
.centered {
  justify-content: center;
}
.cell-icon {
  align-content: center;
  margin-top: 5px;
}
</style>
