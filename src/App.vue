<script setup>
import { ref } from "vue";
const active = ref(0);
</script>

<template>
  <div>
    <nav class="header">去中心化投票APP</nav>
    <main>
      <router-view></router-view>
    </main>
    <van-tabbar route v-model="active" active-color="#2feec8">
      <van-tabbar-item to="/" icon="manager">分发票权</van-tabbar-item>
      <van-tabbar-item to="/account" icon="notes"
        >账户信息</van-tabbar-item
      >
      <van-tabbar-item to="/board" icon="description">投票展板</van-tabbar-item>
    </van-tabbar>
  </div>
</template>

<style lang="less" scoped>
.header {
  height: 44px;
  background-color: #2feec8;
  color: #fff;
  text-align: center;
  line-height: 44px;
}
</style>

<style lang="less">
html,
body {
  margin: 0;
  padding: 0;
  overflow: hidden;
}
</style>
