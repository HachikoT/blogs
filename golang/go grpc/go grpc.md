- [搭建grpc-go环境](#搭建grpc-go环境)
  - [安装protobuf编译器](#安装protobuf编译器)
  - [安装支持protoc的go插件包](#安装支持protoc的go插件包)
- [编写hello world](#编写hello-world)

# 搭建grpc-go环境

## 安装protobuf编译器

protobuf编译器`protoc`程序用来编译`.proto`文件，里面包含了服务和消息的定义，然后将其转换为指定的编程语言代码。

通过下载二进制安装：

```bash
curl -LO https://github.com/protocolbuffers/protobuf/releases/download/v3.15.8/protoc-3.15.8-linux-x86_64.zip
unzip protoc-3.15.8-linux-x86_64.zip -d $HOME/.local
# 修改/etc/profile
export PATH=$PATH:$HOME/.local/bin
```

## 安装支持protoc的go插件包

```bash
go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.28
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.2
```

将go的二进制包也放入`PATH`环境变量，这样`protoc`可以找到那两个插件。

```bash
export PATH="$PATH:$(go env GOPATH)/bin"
```

# 编写hello world

首先编写`hello_world.proto`文件，定义服务和消息。

```proto
syntax = "proto3";

option go_package = "hello/helloworld";

package helloworld;

// 服务定义
service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// 请求消息
message HelloRequest {
  string name = 1;
}

// 回应消息
message HelloReply {
  string message = 1;
}
```

生成代码。

```bash
protoc --go_out=. --go_opt=paths=source_relative --go-grpc_out=. --go-grpc_opt=paths=source_relative helloworld/hello_world.proto
```

编写服务端代码。

```go
package main

import (
	"context"
	"log"
	"net"

	pb "hello/helloworld"

	"google.golang.org/grpc"
)

// 实现服务端rpc接口
type server struct {
	pb.UnimplementedGreeterServer
}

// 实现rpc接口
func (s *server) SayHello(ctx context.Context, in *pb.HelloRequest) (*pb.HelloReply, error) {
	log.Printf("Received: %v", in.GetName())
	return &pb.HelloReply{Message: "Hello " + in.GetName()}, nil
}

func main() {
	lis, err := net.Listen("tcp", ":50051")
	if err != nil {
		log.Fatalf("failed to listen: %v", err)
	}
	s := grpc.NewServer()
	pb.RegisterGreeterServer(s, &server{})
	log.Printf("server listening at %v", lis.Addr())
	if err := s.Serve(lis); err != nil {
		log.Fatalf("failed to serve: %v", err)
	}
}

```

编写客户端代码。

```go
package main

import (
	"context"
	"log"
	"time"

	pb "hello/helloworld"

	"google.golang.org/grpc"
	"google.golang.org/grpc/credentials/insecure"
)

func main() {
	conn, err := grpc.Dial("localhost:50051", grpc.WithTransportCredentials(insecure.NewCredentials()))
	if err != nil {
		log.Fatalf("did not connect: %v", err)
	}
	defer conn.Close()
	c := pb.NewGreeterClient(conn)

	// Contact the server and print out its response.
	ctx, cancel := context.WithTimeout(context.Background(), time.Second)
	defer cancel()
	r, err := c.SayHello(ctx, &pb.HelloRequest{Name: " world"})
	if err != nil {
		log.Fatalf("could not greet: %v", err)
	}
	log.Printf("Greeting: %s", r.GetMessage())
}

```
