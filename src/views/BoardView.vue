<template>
  <div class="box3">
    <van-divider dashed>投票展板</van-divider>
    <van-cell :title="item.name" icon="shop-o" v-for="(item, index) in board">
      <!-- item: 代表当前循环到的数组元素（或者对象属性值）第一个。index: 代表当前循环项的索引（从 0 开始）第二个。 -->
      <template #right-icon>
        <van-button @click="vote(index)">{{ item.totalAmount }}</van-button>
      </template>
    </van-cell>
    <!-- 按钮的点击事件会触发 vote(index) 方法，并显示 item.totalAmount 作为按钮的文本 -->
  </div>
</template>


<script setup>
import useWeb3 from "../hooks/useWeb3";
import { ref, onMounted } from "vue";
const { web3, voteContract, getAccount } = useWeb3();

// 看板信息
const board = ref([]);

const nowAccount = ref("");

const getBoardInfo = async () => {
  const result = await voteContract.methods.getBoardInfo().call();//返回一个对象数组
  board.value = result;
};

const vote = async (index) => {
  const nowAccount = await getAccount();
  const result = await voteContract.methods.vote(index).send({ from: nowAccount });
};

const initEventListen = () => {
  voteContract.events
    .voteSuccess({ fromBlock: 0 }, (err, event) => {// 读取从编号0开始的区块中的事件
      console.log("监听执行");
      console.log(event);
    })
    .on("data", (event) => {//事件监听器,处理 voteContract.events.voteSuccess 事件发出的数据,并执行指定的回调函数
      console.log("智能合约触发的事件：", event.returnValues);
    });
};

onMounted(() => {
  initEventListen();
  getBoardInfo();
});
</script>
<style lang="less"></style>
