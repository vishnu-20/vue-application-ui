<template>
    <div class="table-actions">
      <button @click="markAsRead" class="action-button">Mark as Read</button>
      <button @click="markAsUnread" class="action-button">Mark as Unread</button>
      <button @click="deleteItems" class="action-button delete">Delete</button>
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
    gap: 15px;
    margin-bottom: 10px;
  }
  
  .action-button {
    padding: 8px 12px;
    background-color: #f0f0f0;
    border: 1px solid #d1d1d1;
    border-radius: 4px;
    font-size: 14px;
    font-weight: bold;
    cursor: pointer;
    color: #333;
  }
  
  .action-button:hover {
    background-color: #e0e0e0;
  }
  
  .delete {
    color: #d9534f;
  }
  
  .delete:hover {
    background-color: #f2dede;
  }
  </style>
  