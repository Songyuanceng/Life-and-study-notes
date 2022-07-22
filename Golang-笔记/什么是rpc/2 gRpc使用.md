gRPC 通过 HTTP/2 对流传输提供了大量的支持：

1.  Unary RPC：一元 RPC。
2.  Server-side streaming RPC：服务端流式 RPC。
3.  Client-side streaming RPC：客户端流式 RPC。
4.  Bidirectional streaming RPC：双向流式 RPC

Unary RPC：一元 RPC，也就是是单次 RPC 调用，简单来讲就是客户端发起一次普通的 RPC 请求，响应，是最基础的调用类型，也是最常用的方式。

Server-side streaming RPC：服务器端流式 RPC，也就是是单向流，并代指 Server 为 Stream，Client 为普通的一元 RPC 请求。简单来讲就是客户端发起一次普通的 RPC 请求，服务端通过流式响应多次发送数据集，客户端 Recv 接收数据集。

Client-side streaming RPC：

Bidirectional streaming RPC：