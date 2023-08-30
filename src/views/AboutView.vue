<template>
  <div class="about">
    <div class="d-flex">
      <div class="black_box">{{ followers_count }} followers</div>
      <div class="black_box">
        <p>{{ this.monthly_total_revenue.EUR }} EUR</p>

        <p>{{ this.monthly_total_revenue.USD }} USD</p>
      </div>
      <div class="black_box">
        <p>Best Seller Items</p>
        <p v-for="(item, index) in this.monthly_best_sellers_item" :key="index">
          {{ item.item_name }} .... {{ item.total_revenue }} USD
        </p>
      </div>
    </div>
    <h1>This is Notification page</h1>

    <NotificationItem
      v-for="(item, index) in this.notification_list"
      :key="index"
      v-bind="item"
      @click="toggleNotifStatus(index)"
    >
    </NotificationItem>
  </div>
</template>
<script>
import NotificationItem from "@/components/NotificationItem.vue";
export default {
  name: "AboutView",
  data() {
    return {
      notification_list: Array(),
      next_page_link: null,
      throttleTimer: false,
      followers_count: Number,
      monthly_total_revenue: Object,
      monthly_best_sellers_item: Array(),
    };
  },
  components: {
    NotificationItem,
  },
  methods: {
    getNotificationList: async function () {
      const resp = await fetch("http://127.0.0.1:8000/api/notification/ss", {
        headers: {
          Authorization: "Bearer " + localStorage.getItem("token"),
        },
      });
      if (resp.status === 200) {
        const data = await resp.json();
        this.next_page_link = data.links.next;
        this.notification_list = data.data;
      }
    },
    toggleNotifStatus: async function (index) {
      var vm = this;
      const res = await fetch("http://127.0.0.1:8000/api/notification/ids", {
        method: "POST",
        headers: {
          Authorization: "Bearer " + localStorage.getItem("token"),
        },

        body: JSON.stringify({
          notification_id: vm.notification_list[index].id,
          notification_read: !vm.notification_list[index].is_read,
        }),
      });

      if (res.status == 200) {
        vm.notification_list[index].is_read =
          !vm.notification_list[index].is_read;
      }
    },

    getUserActivities: async function () {
      const resp = await fetch("http://127.0.0.1:8000/api/user/activities", {
        headers: {
          Authorization: "Bearer " + localStorage.getItem("token"),
        },
      });
      const data = await resp.json();
      if (resp.status == 200) {
        this.followers_count = data.monthly_followers;
        this.monthly_total_revenue = data.monthly_total_revenue;
        this.monthly_best_sellers_item = data.monthly_best_sellers_item;
      }
    },
    getNextUser: function () {
      var vm = this;

      window.onscroll = async () => {
        let bottomOfWindow =
          document.documentElement.scrollTop + window.innerHeight + 10 >=
          document.documentElement.offsetHeight;

        if (bottomOfWindow) {
          if (vm.throttleTimer) return;
          vm.throttleTimer = true;
          console.log("salam");
          const resp = await fetch(vm.next_page_link, {
            headers: {
              Authorization: "Bearer " + localStorage.getItem("token"),
            },
          });

          if (resp.status === 200) {
            const data = await resp.json();
            vm.next_page_link = data.links.next;
            this.notification_list.push(...data.data);
          }
        }
        vm.throttleTimer = false;
      };
    },
  },
  beforeMount() {
    this.getNotificationList();
    this.getUserActivities();
  },
  mounted() {
    this.getNextUser();
  },
};
</script>

<style scoped>
.black_box {
  padding: 10px;
  background-color: darkslategrey;
  color: azure;
  display: block;
  width: 300px;
}
.d-flex {
  display: flex;
  justify-content: space-around;
}
</style>
