<template>
  <div>
    <div v-for="img in imgList" :key="img.pk" class="users">
      <img :src="apiUrl + 'upload/' + img.headImg" alt />
      <p>{{ img.nikeName }}</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      apiUrl: "http://127.0.0.1:9000/",
      imgList: [],
      menuId: 1
    };
  },

  mounted() {
    this.getUserList(this.menuId);
  },
  watch: {
    $route(to) {
      console.log(to);
      this.menuId = to.query.menuId;
      this.getUserList(this.menuId);
    }
  },

  methods: {
    getUserList(id) {
      console.log(id);
      axios({
        url: "http://127.0.0.1:9000/get-user-list/",
        type: "json",
        params: {
          id
        },
        method: "get"
      }).then(res => {
        console.log(res);
        this.imgList = res.data
      });
    }
  }
};
</script>

<style>
</style>