<template>
<div> 
    <!-- 导航栏 -->
    <nav>
        <el-menu :default-active="activeIndex" class="el-menu-demo" mode="horizontal" @select="handleSelect" >
        
        <el-menu-item index="1"><img alt="Vue logo" :src="icon" style="height:100%"/></el-menu-item>
        <el-menu-item index="2" v-if="loginStatus == true">关注</el-menu-item>
        <el-menu-item index="3" v-if="loginStatus == true">消息</el-menu-item>
        <el-menu-item index="4">
            <el-input placeholder="请输入内容" v-model="searchKey" class="input-with-select">
                <el-button slot="append" icon="el-icon-search" circle @click="search(searchKey)"></el-button>
            </el-input>
        </el-menu-item>

        <el-menu-item index="5" v-if="loginStatus != true">登录</el-menu-item>
        <el-menu-item index="6" v-if="loginStatus != true">注册</el-menu-item>
         <el-submenu index="7" v-if="loginStatus == true">
            <template slot="title">我的工作台</template>
            <el-menu-item index="7-1">我的主页</el-menu-item>
            <el-menu-item index="7-2">收藏的文章</el-menu-item>
            <el-menu-item index="7-3">喜欢的文章</el-menu-item>
            <el-menu-item index="7-4">设置</el-menu-item>
            <el-menu-item index="7-5">退出</el-menu-item>
        </el-submenu>
        <el-menu-item index="8">写文章</el-menu-item>

        </el-menu>
    </nav> 
    <!-- 路由 -->
    <router-view></router-view>
    <!-- 消息提示 -->
    <el-dialog
      title="提示"
      :visible.sync="dialogVisible"
      width="30%"
      >
      <span>你还未登录，是否需要登录</span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogVisible = false;goLogin();">确 定</el-button>
      </span>
    </el-dialog>
</div>
</template>
<style scoped>
   nav{
    padding-left: 20%

  } 
</style>

<script>

import logo from '@/assets/logo.png'
export default {
  data() {
    return {
      icon:logo,
      path: [
        "",
        "/",
        "/follow",
        "/message",
        "",
        "/login",
        "/register",
        "",
        "/write"
      ],
      mypath: ["", "/myHomePage", "/myCollection", "/myLike", "/setting"],
      activeIndex: "1",
      loginStatus: false,
      searchKey: "",
      isdesable: false,
      dialogVisible: false
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      let name = this.$cookieStore.getCookie("token");
      if (name != null) {
        this.loginStatus = true;
      } else {
        this.loginStatus = false;
      }
      //   this.isdesable = false;
    },
    search(searchKey) {
      //TODO 搜索
      // this.$message('正在搜索\"'+searchKey+'\"的值');
      if (searchKey.length > 0) {
        this.$router.push("/" + searchKey + "/search");
      }
      this.isdesable = false;
    },
    handleSelect(key, keyPath) {
      //console.log(key);
      //定义路由
      if (key.substr(0, 1) == "7") { //我的账号路由
        if (key.substr(key.length - 1) == 5) {
          this.$cookieStore.delCookie("token");
          this.loginStatus = false;
          this.$router.push("/");
        } else {
          this.$router.push(this.mypath[key.substr(key.length - 1)]);
        }
      } else if (key != 4) {//除搜索
        //未登录点击写文章事件
        if (key == 8 && this.loginStatus == false) {
          this.dialogVisible =true;
        }else{
          this.$router.push(this.path[key]);
        }
        
      }
    },
    goLogin(){
      this.$router.push("/login");
    }

  }
};
</script>

