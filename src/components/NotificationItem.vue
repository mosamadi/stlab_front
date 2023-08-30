<template>
  <div class="notification" @click="changeNotificationStatus">
    <div :class="[is_read ? 'read' : 'unread']">
      <p>{{ msg }}</p>
      <i v-if="type === 'donation'" class="fas fa-hand-holding-usd"></i>
      <i v-else-if="type === 'merch_sale'" class="fas fa-shopping-cart"></i>
      <i v-else-if="type === 'follower'" class="fas fa-user-friends"></i>
      <i v-else class="fas fa-file-invoice"></i>
    </div>
  </div>
</template>

<script>
export default {
  name: "NotificationItem",
  props: ["id", "msg", "is_read", "type"],
};
</script>

<style scoped>
.notification {
  border: 1px solid #ccc;
  cursor: pointer;
  width: 300px;
  min-height: 50px;
  padding: 10px;
  margin: 5px;
}
.unread {
  border-left: 5px red solid;
}

.read {
  border-left: 5px green solid;
}
</style>
