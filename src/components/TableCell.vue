<template>
    <td :class="{ odd: isOddRow }" :style="{ color: color }">
        <div :class="{ wrapper: true, centered: hasIcon }">
            <span v-if="selectable">
                <input
                    type="checkbox"
                    :checked="isSelected"
                    @change="toggleSelection"
                />
            </span>
            <span class="text-container">
                <span v-if="!hasLink" class="text" v-html="fieldValue"></span>
                <a v-if="hasLink" class="link" :href="link" @click="handleOpenModal">
                    {{ fieldValue }}
                </a>
            </span>
        </div>
    </td>
</template>
  <script>
  export default {
    name: "TableCell",
    components: {  },
    props: {
        index: Number,
    fieldValue: { type: String, default: "" },
    link: { type: String, default: "" },
    iconLink: { type: String, default: "" },
    fieldIcon: { type: String, default: "" },
    width: { type: Number, default: 100 },
    color: { type: String, default: "#292E31" },
    selectable: { type: Boolean, default: false },
    isSelected: { type: Boolean, default: false },
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
  },
  methods: {
    toggleSelection() {
      this.$emit("toggleRowSelection", this.index);
    },
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
}td a {
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
.icon {
  :deep(svg) {
    width: 20px;
    height: 20px;
  }
  :deep(path) {
    fill: #267abd;
  }
  margin-top: 4px;
}
</style>