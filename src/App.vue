<template>
  <div id="home">
    <div>
      <button @click="showLoginResBox(1)">登录</button>
      <button @click="showLoginResBox(2)">注册</button>
      <button @click="showLoginResBox(3)">修改</button>
      <div class="header">  
        <h1>{{ siteInfo.site_title }}</h1>
        <img :src="siteInfo.site_logo" alt />
      </div>
      <hr />

      <div class="content">
        <div class="menu">
          <div v-for="menu in menuList" :key="menu.id" class="item">
            <div v-if="menu.id==choosed" style="background: #777; color: #fff">
              <a style="color: #fff">{{ menu.text }}</a>
            </div>
            <div v-else @click="chooseMenu(menu.id)">
              <a style="color: #000">{{ menu.text }}</a>
            </div>
          </div>
        </div>

        <div class="userlist">
          <p>{{ choosed_text }}</p>
          <hr />
          <router-view />
        </div>
      </div>
    </div>
  <LoginBox v-if="loginTagers" :target="loginTagers" @hideLogin="hideLoginResBox"></LoginBox>  
  </div>
  
</template>

<script>
import axios from "axios";
import LoginBox from "../src/components/LoginBox"
export default {
  components:{
    LoginBox
  },
  data() {
    return {
      menuList: [],
      choosed: 1,
      choosed_text: "Django后端",
      loginTagers: 0,
      siteInfo: {}
    };
  },
  mounted() {
    this.getMenu();
  },
  methods: {
    showLoginResBox(value){
      this.loginTagers = value;
    },
    hideLoginResBox(){
      this.loginTagers = 0
    },
    getMenu() {
      axios({
        url: "http://127.0.0.1:9000/get-menu-list/",
        type: "json",
        method: "get"
      }).then(res => {
        console.log(res.data);
        this.menuList = res.data.menu_data;
        this.siteInfo = res.data.site_data;
      });
    },
    chooseMenu(id) {
      console.log(id);
      this.choosed = id;
      for (let index = 0; index < this.menuList.length; index++) {
        if (id == this.menuList[index].id) {
          this.choosed_text = this.menuList[index].text;
        }
      }
      this.$router.push({ path: "/", query: { menuId: id } });
    }
  },
  
};
</script>

<style>

</style>
