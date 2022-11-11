# ethereum-connect-metamask
这是一个用 window.ethereum 接口来连接与操作 MetaMask 钱包的项目例子。运行该项目，需要先安装 MetaMask 钱包。

访问 [这里](https://www.benmo.cc) 获取更多区块链资料。

## 安装 MetaMask 钱包
[MetaMask](https://metamask.io/) 是一个浏览器插件钱包，支持火狐浏览器（[Firefox](https://www.mozilla.org/)）和谷歌浏览器（[Chrome](https://www.google.cn/intl/zh-CN/chrome/)）。安装完成后，window 对象中会出现一个 window.ethereum 的对象。

## 项目依赖
项目依赖库如下：
- [Bootstrap](https://v3.bootcss.com/)：一个优秀的前端 UI 框架；
- [Jquery](https://jquery.com/)：前端 Javascript 框架；
- window.ethereum：以太坊接口对象，用于连接和操作 MetaMask 钱包。

## 创建项目

首先，创建一个 Web 项目。该项目采用 html + javascript 开发，前端页面采用 Bootstrap 框架。

在项目中，我们创建一个前端 html 页面，命名为 index.html。

在页面文件引入 Bootstrap 依赖（包括 css 样式文件和 js 文件）：

```
<link href="assets/css/bootstrap.min.css" rel="stylesheet">
<script src="assets/js/bootstrap.min.js"></script>
```

在页面文件中 引入 Jquery 依赖：

```
<script src="assets/js/jquery-3.3.1.min.js"></script>
```

页面设计这里不做介绍，本文我们重点介绍接口开发部分。

## 接口开发

本项目介绍了几个基本的接口用法，更多的接口开发请参考 [MetaMask 开发者指南](https://docs.metamask.io/guide/)。

### 钱包检测

页面初始化时，检测是否已安装 MetaMask，如果没有则提示安装。代码如下：

```
if (typeof window.ethereum === 'undefined') {
	console.log("Please install MetaMask.");
} else {
	// app init code
}
```

### 连接钱包

连接钱包，获取当前激活的账号。代码如下：

```
const connectMetamask = document.getElementById('connectMetamask');
connectMetamask.addEventListener('click', () => {
	// 授权链接MetaMask
	ethereum
        .request({ method: 'eth_requestAccounts' })
        .then(handleAccountsChanged)
        .catch((err) => {
            if (err.code === 4001) {
                console.log('Please connect to MetaMask.');
            } else {
                console.error(err);
            }
        });
}
```

连接钱包时需要 MetaMask 授权。

`handleAccountsChanged` 是自定义的回调函数，代码如下：

```
var account = null;
function handleAccountsChanged(accounts) {
	if (accounts.length === 0) {
		console.log('Please connect to MetaMask.');
	} else if (accounts[0] !== account) {
		account = accounts[0];
	}
}
```

以上代码判断是否连接成功，连接成功后重置当前账号。

### 获取账号

获取当前激活的账号。代码如下：

```
const showAccount = document.getElementById('showAccount');
showAccount.addEventListener('click', () => {
	ethereum.request({ method: 'eth_accounts' }).then((result) => {
		console.log(result);
	});
});
```

### 获取链 ID

获取当前激活的区块链网络的链 ID。代码如下：

```
const showChainId = document.getElementById('showChainId');
showChainId.addEventListener('click', () => {
    ethereum.request({ method: 'eth_chainId' }).then((result) => {
    	console.log(result);
   	});
});
```

### 获取公钥

获取当前激活账号的公钥。代码如下：

```
const showPublicKey = document.getElementById('showPublicKey');
showPublicKey.addEventListener('click', () => {
    // 授权提供公钥
    ethereum
    	.request({ method: 'eth_getEncryptionPublicKey', params: [account] })
    	.then((result) => {
    		console.log(result);
    	})
    	.catch((err) => {
    		if (err.code === 4001) {
    			console.log('Please connect to MetaMask.');
    		} else {
    			console.error(err);
    		}
    	});
});
```

获取公钥时需要 MetaMask 授权。

### 监听账号变更

用于监听钱包账号的变更。代码如下：

```
ethereum.on('accountsChanged', (accounts) => {
	handleAccountsChanged(accounts);
});
```

钱包地址变更后需要重新获取当前激活的账号。

### 监听区块链网络变更

用于监听区块链网络的变更。代码如下：

```
ethereum.on('chainChanged', (chainId) => {
	window.location.reload();		//刷新
	// todo
});
```

建议变更区块链网络后重新刷新页面进行初始化。

## 项目部署

项目在 Apache 或 IIS 等 Web 服务中部署，打开浏览器访问。
