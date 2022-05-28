## solidity编写智能合约配合vue3进行交互



### 准备工作

+ remix - 在线编写智能合约，支持编译与部署
+ metamask - 一款谷歌钱包插件
+ ganache - 提供本地测试账户



### 编写一个简单的合约

```solidity
// SPDX-License-Identifier: GPL-3.0

pragma solidity >=0.7.0 <0.9.0;


contract Counter {
    uint private count;

    constructor() {
        count = 0;
    }

    function countAdd() public {
        count++;
    }

    function countGet() public view returns(uint) {
        return count;
    }
}
```

写完之后记得去编译一下.

#### 接着去部署一下合约：

选择环境：

**Web3 Provider** 指向你本地的测试环境 HTTP://127.0.0.1:9545



### 与Vue3 交互

> 环境准备：
>
> 先去搭建一个 vue3+ts 的开发环境
>
> 安装好 `ethers`  --yarn add ethers`



#### 1. 先去提供一个 合约和web交互的一个环境 - Provider

```js
const Provider = () => {
  return new ethers.provider.Web3Provider(window.ethereum)
}
```



#### 2.连接Metamask，展示账户地址

```js
const checkLogin = async () => {
  const {ethereum} = window
  if (!ethereum) {
    alert('请先安装好metamask钱包')
    return 
  } 
  const [account] = await Provider().send('eth_requestAccounts', [])
  userAddress.value = account
}
```



#### 3. 获取之前写好的合约

```js
const Contract = () => {
  const provider = Provider()
  // signer 用户 => 签名，信息
  const signer = Provide.getSigner()
  // ethers.Contract 接收（contractAddress, contractABI, signer）
  const Contract = new ethers.Contract(contractAddress, contractABI, signer)
  return Contract
}
```



#### 4. 可以去调用合约上的一些方法

```js
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
```







