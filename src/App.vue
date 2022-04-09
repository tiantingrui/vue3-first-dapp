<template>
  <img
    alt="Vue logo"
    src="./assets/logo.png"
  />

  <div v-if="!userAddress">
    <button @click="checkLogin">Login</button>
  </div>
  <div>{{ userAddress }}</div>
  <div v-if="userAddress">
    <div>
      <h1>👋🏻 {{count}}</h1>
    </div>
    <div>
      <button @click="addCount"> say hi </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { ethers } from 'ethers'
import { contractABI, contractAddress } from './contract'

const userAddress = ref('')
const count = ref(0)

// provider 合约和web交互的一个环境的提供者
const Provider = () => {
  return new ethers.providers.Web3Provider(window.ethereum)
}

const checkLogin = async () => {
  const { ethereum } = window
  if (!ethereum) {
    alert('请安装metamask钱包')
    return
  }
  const [account] = await Provider().send('eth_requestAccounts', [])
  userAddress.value = account
}

const Contract = () => {
  const Provide = Provider()
  // signer 用户 => 签名，信息
  const signer = Provide.getSigner()
  // ethers.Contract 接收（contractAddress, contractABI, signer）
  const Contract = new ethers.Contract(contractAddress, contractABI, signer)
  return Contract
}

const getCount = async () => {
  const event = Contract()
  const rst = await event.countGet()
  count.value = rst.toNumber()
}

const addCount = async () => {
  const event = Contract()
  const tx = await event.countAdd()
  // 等待matemask交易完毕
  await tx.wait()
  getCount()

}

getCount()



</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
