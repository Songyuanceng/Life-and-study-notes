1、什么是 RPC
RPC 代指远程过程调用（Remote Procedure Call），它的调用包含了传输协议和编码（对象序列）协议等等，允许运行于一台计算机的程序调用另一台计算机的子程序，而开发人员无需额外地为这个交互作用编程，因此我们也常常称 RPC 调用，就像在进行本地函数调用一样方便。

2、gRPC 是一个高性能、开源和通用的 RPC 框架，面向移动和基于 HTTP/2 设计。目前提供 C、Java 和 Go 语言等等版本，分别是：grpc、grpc-java、grpc-go，其中 C 版本支持 C、C++、Node.js、Python、Ruby、Objective-C、PHP 和 C# 支持。

gRPC 基于 HTTP/2 标准设计，带来诸如双向流、流控、头部压缩、单 TCP 连接上的多复用请求等特性。这些特性使得其在移动设备上表现更好，在一定的情况下更节省空间占用。

gRPC 的接口描述语言（Interface description language，缩写 IDL）使用的是 Protobuf，都是由 Google 开源的。

3、流程图
![[Pasted image 20220307164548.png]]