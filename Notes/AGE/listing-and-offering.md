项目要求：

    自动为opensea上的 AGE NFT 创造流水和交易额

代码路径：

    1 - 安装 node.js opensea.js web3
    2 - nodejs项目的一般写法


Some BUGs & high-lights:

    1 - nodejs项目中npm类似于maven，是管理项目的依赖用的；所以用npm安装时包是安装到项目内部的。项目之间的包不共享
    2 - hardhat利用插件形成框架的服务
    3 - hardhat task 


目前有两种方案：

    StackOver Flow 上寻找 Opensea js 的正确用法
    直接和 wyv 合约交互看是否有可能达成任务

*subproviders*: 一种钱包 RPC ？

*mnemonic*: 助记词

# *About wallet*
## 工作原理

privateKey and publicKey

privateKey => signature

## 常见形态

    1. Private Key
    2. Keystore and Password
    3. Mnemonic Seed

## *Mnemonic Generating*

    1. 随机产生 128/256 位的随机数
    2. 经过一定处理方法生成助记词
    3. 根据助记词 PBKDF2 生成种子
    4. 种子经过 HMAC-SHA512 生成母密钥
    5. 通过 CKD 母密钥生成众多子密钥
    6. 利用私钥 (用户自定义密码) 加密生成 keystore

## *BIP* 协议 （*Bitcoin Improvement Protocals*）

    m/purpose'/cointype'/account'/change/address_index
    目前市面上的ETH钱包采用以上 Bitcoin HD Wallet 架构， BIP44 like:
    m/44'/60'/0'/0/0

## *node: process.env*

    常用于环境变量
    process 是一个全局变量，提供相关的信息
    process.env 返回一个包含用户环境信息的对象
    
*so, how to set environment configures in linux*

    export *** = ###
    export *** = #***:###

## *json-rpc-engine* usage method

    - will be revealed later

## *Opensea Operation*

*make offer*

    一般选择 WETH 作为价值标的，也可以自定义 ERC-20 代币作为标的
    不可以用 ETH 作为价值标的
    第一次 make offer 之前需要和 opensea 签署一个合约，这一步骤需要花费一定 gas fee
    后面可以继续挂单，这也需要在钱包上签署一个合约，但是这一步骤不要花费任何 gas fee

*make sell*

    最开始需要签两份合约，这一步骤需要花费 gas fee
    一般选择 ETH 作为价值标的
    后面 sell 需要在钱包上签署一个合约，但是这一步骤不需要花费 gas fee

*accept offer*

    直接点击确认即可,需要花费 gas fee
    如果 offer 提供者在 accept 之前账户中的代币已经不足，则 offer 会自动取消

*accept sell*

    直接点击确认即可,需要花费 gas fee

每笔成交的交易，opensea 会给 owner 收取 2.5% 的手续费。交易发起者需要承担相应的 gas fee.

## 交易流程详解 —— Wyv protocol

    - TODO

## Swap

    1 - 通过 ERC-20 合约购买
    2 - 通过 Uni-Swap 交换

## 一些常用的接口

    const contractInstance = new web3.eth.Contract(
        contractABI,
        "0x contract address",
    );

    contractInstance.methods.yourFunction().call()
        .catch((err)=>{console.log(err);})

    contractInstance.methods.anotherFunction().
    send().once().on().on()

    
 