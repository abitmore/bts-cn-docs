cli_wallet 也可以连接到公共 API 节点使用，格式类似如下：

```
cli_wallet -s wss://api.bts.mobi/
```

公共 API 节点清单在 https://github.com/bitshares/bitshares-ui/blob/master/app/api/apiConfig.js 里搜索 url 。

（注： 4.0 版本已经修复了下面的连接问题）

另外， Windows 下，有个 SSL 证书问题导致连不上 wss 开头的 API 节点。

解决步骤：

1. 下载 https://curl.haxx.se/ca/cacert.pem 比如保存为 c:\cacert.pem

2. 在命令行窗口里执行
```
set SSL_CERT_FILE=c:/cacert.pem
cli_wallet -s wss://bitshares.openledger.info/ws
```
相关信息见 https://github.com/bitshares/bitshares-core/issues/314

还有些问题，比如 windows 下可能连接断掉但程序不退，退出时会弹窗报错，等等，但不影响一般使用。
