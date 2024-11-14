<template>
  <div class="table-actions">
    <div @click="markAsRead" class="action-button">Mark as Read</div>
    <div @click="markAsUnread" class="action-button">Mark as Unread</div>
    <div @click="deleteItems" class="action-button delete">Delete</div>
  </div>
</template>

<script>
export default {
  methods: {
    markAsRead() {
      this.$emit('mark-as-read');
    },
    markAsUnread() {
      this.$emit('mark-as-unread');
    },
    deleteItems() {
      this.$emit('delete-items');
    },
  },
};
</script>
<style scoped lang="scss">
.table-actions {
  display: flex;
  gap: 6px;
  margin-bottom: 10px;
}

.action-button {
  padding: 8px;
  font-size: 14px;
  cursor: pointer;
  color: #777c7f;
}

.action-button:hover {
  background-color: #e0e0e0;
}
</style>

