<template>
  <div id="login" @click.self="hideSelf">
    <div id="loginbox">
      <div class="form">
        <div v-if="target==1 || target==2" class="item">
          <div class="span">用户名：</div>
          <input v-model="username" type="text" placeholder="输入用户名" />
        </div>
        <div v-if="target==1 || target==2" class="item">
          <div class="span">密码：</div>
          <input v-model="password" type="text" placeholder="输入密码" />
        </div>
        <div v-if="target==2" class="item">
          <div class="span">重复密码：</div>
          <input v-model="password2" type="text" placeholder="请重新输入密码" />
        </div>
        <div v-if="target==3" class="item">
          <div class="span">网站名称：</div>
          <input v-model="sitename" type="text" placeholder="请输入网站名称" />
        </div>
        <div v-if="target==3" class="item">
          <div class="span">上传图片：</div>
          <input id="uploadLogo" @change="uploadImg($event)" type="file" style="width: 180px" />
        </div>
        <div v-if="target==3" class="item">
          <img :src="siteLogo" alt />
        </div>
        <button v-if="target==1" @click="toLogin">登录</button>
        <button v-if="target==2" @click="toRegister">注册</button>
        <button v-if="target==3" @click="toUpload">确定</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Qs from "qs";
export default {
  name: "LoginBox",
  props: ["target"],
  data() {
    return {
      username: "",
      password: "",
      password2: "",
      sitename: "",
      siteLogo: ""
    };
  },
  mounted() {
    console.log(this.target);
  },
  methods: {
    hideSelf() {
      this.$emit("hideLogin");
    },
    toUpload() {
      var sitename = this.sitename;
      var siteLogo = this.siteLogo;
      console.log(sitename);
      if (sitename.length > 0 && siteLogo.length > 0) {
        axios({
          url: "http://127.0.0.1:9000/uploadImg/",
          headers: {
            "Content-Type": "application/x-www-form-urlencoded"
          },
          method: 'put',
          data: Qs.stringify({
            sitename,
            siteLogo
          })
        }).then((res)=>[
          console.log(res),
          window.location.reload()
        ])
      } else {
        alert("请输入必传项");
      }
    },
    uploadImg(e) {
      var logo = e.target.files[0];
      var Img = new FormData();
      Img.append("logo", logo);
      axios({
        url: "http://127.0.0.1:9000/uploadImg/",
        method: "post",
        headers: {
          "Content-Type": "application/x-www-form-urlencoded"
        },
        data: Img
      }).then(res => {
        console.log(res.data);
        if (res.data) {
          this.siteLogo = "http://127.0.0.1:9000/upload/" + res.data.site_logo;
        }
      });
    },
    toRegister() {
      var username = this.username;
      var password = this.password;
      var password2 = this.password2;
      if (username.length > 0 && password.length > 0 && password2.length > 0) {
        if (password2 != password) {
          alert("两次密码不一致");
        } else {
          axios({
            url: "http://127.0.0.1:9000/register/",
            headers: {
              "Content-Type": "application/x-www-form-urlencoded"
            },
            data: Qs.stringify({
              username,
              password,
              password2
            }),
            method: "post"
          }).then(res => {
            console.log(res.data);
            switch (res.data) {
              case "00000":
                alert("注册成功");
                break;
              case "10000":
                alert("用户已存在");
                break;
            }
          });
        }
      } else {
        alert("请输入必输参数");
      }
    },

    toLogin() {
      console.log(this.username, this.password);
      axios({
        url: "http://127.0.0.1:9000/login/",
        headers: {
          "Content-Type": "application/x-www-form-urlencoded"
        },
        data: Qs.stringify({
          username: this.username,
          password: this.password
        }),
        method: "post"
      }).then(res => {
        switch (res.data) {
          case "0001":
            alert("账号密码错误");
            break;
          case "0002":
            alert("用户名不存在");
            break;
          case "0003":
            alert("账号不能为空");
            break;
          case "0004":
            alert("密码不能为空");
            break;
          case "0005":
            alert("请输入账号密码");
            break;
          default:
            console.log(res.data.token);
            alert("登录成功");
        }
      });
    }
  }
};
</script>

<style>
#login {
  position: absolute;
  top: 0;
  width: 700px;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #00000020;
}

#loginbox {
  width: 300px;
  height: 300px;
  background: #00000070;
  border: 1px solid #000000;
  color: #ffffff;
  display: flex;
  justify-content: center;
  align-items: center;
}

#loginbox .form .item {
  font-weight: 700;
  margin: 10px auto;
}

#loginbox .form .item .span {
  float: left;
  width: 100px;
}
</style>

