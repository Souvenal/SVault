# TLS

传输层安全协议，用于建立安全连接。

## 握手流程

```
客户端                    服务器
   |                         |
   |---- 1. Client Hello --->|  支持的加密方式
   |                         |
   |<--- 2. Server Hello ----|  选定的加密方式
   |<--- 3. Certificate -----|  服务器证书
   |<--- 4. Server Done -----|  服务器准备完成
   |                         |
   |---- 5. Client Key ----->|  密钥交换信息
   |---- 6. Finished ------->|  客户端准备完成
   |                         |
   |<=======================>|  开始加密通信
```

## Client Hello

### SNI (Server Name Indication)

TLS 扩展字段，指定访问域名，在 Client Hello 阶段发送。

> 为什么需要 SNI？
> 一个服务器可能托管多个网站，共享 IP 地址。TLS 握手时服务器需要先发送证书，由 SNI 指示访问域名。
