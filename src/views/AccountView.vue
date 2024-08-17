<template>
  <div class="box2">
    <van-divider>账户信息</van-divider>
    <!-- 存放当前账户 -->
    <van-space>  
      <p class="label">当前账户</p>
      <p class="address">{{ nowAccount }}</p>
    </van-space>
    <br />
    <!-- 存放当前账户票数 -->
    <van-space>
      <p class="label">账户票数</p>
      <p class="address">{{ voterInfo.amount }}</p>
    </van-space>
    <br />
    <!-- 存放当前账户的委托投票账户 -->
    <van-space>
      <p class="label">委托账户</p>
      <p class="address">{{ voterInfo.delegator }}</p>
    </van-space>
    <br />
    <!-- 显示当前账户是否已经投票 -->
    <van-space>
      <p class="label">是否已投票</p>
      <p class="address">{{ voterInfo.isVoted }}</p>
    </van-space>
    <br />
    <!-- 显示当前账户的投票地址 -->
    <van-space>
      <p class="label">投票ID</p>
      <p class="address">{{ voterInfo.targetId }}</p>
    </van-space>
    <br />
    <!-- 存放委托他人投票的逻辑 -->
    <div class="btn">
      <van-cell-group inset>
        <van-field
          v-model="delegatorAddress"
          label="受托人地址"
          placeholder="请输入受托人地址"
        />
      </van-cell-group>
      <van-button block @click="delegate">委托他人代投</van-button>
    </div>
  </div>
</template>


<script setup>
import { ref, onMounted } from "vue";
import useWeb3 from "@/hooks/useWeb3";
const { web3, voteContract, getAccount } = useWeb3();
//存放当前账户
const nowAccount = ref("");
// 选民信息（当前账户）
const voterInfo = ref({});
// 受托人地址
const delegatorAddress = ref("");


const getVoteInfo = async () => {
  nowAccount.value = await getAccount();//获取当前账户
  voterInfo.value = await voteContract.methods.voters(nowAccount.value).call();//获取选民信息（当前账户），voters是一个映射，通过地址可以映射到Voter对象
};

//委托交互
const delegate = () => {
  voteContract.methods
    .delegate(delegatorAddress.value)
    .send({ from: nowAccount.value })//用于发送一个交易，实际将调用 delegate 方法并且修改区块链状态。
    .on("receipt", (receipt) => {//监听交易的回执，并触发回调函数 
      if(receipt.status){//交易成功为true
      console.log("委托成功！");
      }
    });
};

onMounted(async () => {
  await getVoteInfo();
});
</script>

<style lang="less"></style>
