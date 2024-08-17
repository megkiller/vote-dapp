<template>
  <div class="box1">
    <van-divider>分发票权</van-divider>
    <div class="host">
      <van-space>
        <p class="label"><van-icon name="manager" />主持人地址</p>
        <p class="address">{{ host }}</p>
      </van-space>
    </div>
    <div>
      <van-space>
        <p class="label"><van-icon name="friends-o" />投票人地址</p>
        <textarea class="votors" v-model="voterAddress"></textarea>
      </van-space>
    </div>
    <div class="btn">
      <van-button block @click="mandate">开始分发选票</van-button>
    </div>
  </div>
</template>


<script setup>
import useWeb3 from "../hooks/useWeb3";
import { ref, onMounted } from "vue";
const { web3, voteContract, getAccount } = useWeb3();

// 定义主持人信息
const host = ref("");
// 获取主持人信息
const getHost = async () => {
  host.value = await voteContract.methods.host().call();//使用 voteContract 实例调用合约的 host() 方法，并将结果赋值给 host.value。host.value 是 Vue 的响应式数据，自动更新界面上的相应部分。methods 是访问合约方法的接口。host() 是智能合约中的一个公共函数（通常是一个公共变量的 getter），call() 用于调用该函数以读取合约状态。call() 是一个只读操作，不会改变合约状态。
};


// 选民地址
const voterAddress = ref("");
// 分发票权
const mandate = async () => {
  const arr = eval(voterAddress.value);//字符串里面是一个数组，给他转化成实际数组
  const nowAccount = await getAccount();//使用 getAccount 函数获取当前用户的以太坊账户。
  voteContract.methods
    .mandate(arr)
    .send({ from: nowAccount })//从当前以太坊账户想合约发送交易
    .on("receipt", (receipt) => {
      if(receipt.status){
        console.log("选票分发成功");
      }
      
    });
};

onMounted(async () => {//在组件挂载即执行 getHost 函数，以便在组件加载时获取并显示主持人信息。
  await getHost();
});
/*
  0x8e93aD3E8EDbEa8f9C7e9b17b79d4E8bFD645e54主持人
  0xB04f65e5a5A9D89b72c6A757E62672CA822F788c选民1
  0x44D00916Ef6f85731BDcECc33cB270F9bFB3177D选民2
  0x906A6242054507E6FbfA314D08bC1bDf6682Dd9e选民3
  ["0xB04f65e5a5A9D89b72c6A757E62672CA822F788c","0x44D00916Ef6f85731BDcECc33cB270F9bFB3177D","0x906A6242054507E6FbfA314D08bC1bDf6682Dd9e"]
  选民数组
*/
</script>
<style lang="scss"></style>
