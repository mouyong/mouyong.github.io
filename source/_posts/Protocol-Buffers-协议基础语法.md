---
title: Protocol Buffers 协议基础语法
date: 2019-08-18 00:31:57
categories: 开发
tags: Protocol-Buffers
english_title: Protocol-Buffers-Protocol-Basic-Grammar
---

# Protocol Buffers 协议基础语法

## 前言

继上一节介绍了 Protocol Buffers 的相关信息后，我们了解到协议的一些特性，优势。

接下来让我们简单学习下 Protocol Buffers 的语法知识。

## 正文

### 1. 注释

使用 C/C++ 语法的注释风格 `//`（单行注释）和 `/* ... */`（多行注释）向 proto 文件中添加注释。

### 2. 语法说明

```
// 使用 proto3 语法
syntax = "proto3";


// 定义 SearchRequest 协议缓冲区数据结构（后文简称消息消息）
message SearchRequest {
  string query = 1; // 指定 query 参数的类型为 string，并分配字段编号为 1.
  int32 page_number = 2; // 指定 page_number 参数的类型为 int32，并分配字段编号为 2.
  int32 result_per_page = 3; // 指定 result_per_page 参数的类型为 int32，并分配字段编号为 3.
}

// 定义 SearchResponse 消息
message SearchResponse {
 ...
}
```

**更多参数类型详见 [协议缓冲区语言指引第三版] Scalar Value Types（标量值类型）部分内容。**

注：
- 每一个字段都有自己唯一的字段编号，这些字段编号用于二进制格式中标识你的字段信息。
- 字段编号的分配从 1 开始。
- 字段编号 1 ~ 15 采用 1 个字节进行编码，16 ~ 2047 采用 2 个字节进行编码。
- 多语言类型可以添加在单个 `.proto` 文件中。
- 消息类型可以进行嵌套。
- 每个消息中的参数可以有 0 个或 1 个信息，这是 proto3 的默认语法。
- repeated 表明指定字段可以重复任意次数（包括 0 次）。
- reserved 指定保留已移除的字段编号或字段名（见下方示例）。
- 标量值类型与语言对应参考表见附录 [协议缓冲区语言指引第三版] 语言指引 Scalar Value Types（标量值类型）部分内容。
- 消息的默认值通常是 空值（string）、空字节（bytes）、零值（numeric）、假值（bool）、
- 枚举值的第一个值为默认值，且必须定义为 0 值，便于兼容 proto2 协议枚举值语法规则。
- 重复字段默认是空的，通常为对应语言的空列表。
- 布尔值的默认值为假值，无法判断是否显示（开发者主动将该值）设置为假值，请尽量避免这种情况的出现，如有需要，请考虑使用其他类型值进行参数定义。
- 标量的类型值为默认值时，不会对该字段进行序列化操作。
- 在枚举类型中，指定 `allow_alias` 为 true，允许该枚举类型中出现同一字段值。
- import 可以导入其他消息定义。
- 消息定义中可以使用其他消息做类型。
- 消息协议的更新有着一些默认规则，详见 [协议缓冲区语言指引第三版] 语言指引 Updating A Message Type 部分内容。
- 未知类型总是会被序列化输出，在 proto3 协议中，未知类型会被忽略，在 3.5 及更高的版本中，解析期间未知字段会被保留。
- Any 任意类型需要导入 `import "google/protobuf/any.proto";` 协议文件。
- oneof 可以指定字段为多种类型
- oneof 不可以指定 repeated 重复。
- oneof 在更新值时，已有的值会被清空。
- 关联映射类型不可以指定 repeated 重复。
- 添加包名有助于避免生成的源代码冲突，在生成源代码时，报名会被翻译成对应语言的命名空间（模块、包）。
- JSON 类型映射默认使用小驼峰命名规则进行消息序列化，在字段定义时使用 `json_name` 可更改为制定的 JSON 字段名。
- 通过定义 service 可以开启 消息协议的 RPC（Remote Procedure Call）调用。
- php 的 rpc 服务端目前还没有较为合适的实现，暂时做 rpc 客户端使用。
- 一些其他语言的可选选项详见附录 [协议缓冲区语言指引第三版] 语言指引 Options 部分内容。


### 3. 消息示例

保留值与保留类型示例

```
// 定义 Foo 协议缓冲区数据结构
message Foo {
  reserved 2, 15, 9 to 11; // 通过字段编号指定保留字段信息
  reserved "foo", "bar"; // 通过字段名自定保留字段信息
}
```


枚举类型示例

```
message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
  // 定义枚举类型 Corpus
  enum Corpus {
    UNIVERSAL = 0; // 定义默认值 UNIVERSAL（默认值为 0，且必须是枚举类型第一个元素）
    WEB = 1;
    IMAGES = 2;
    LOCAL = 3;
    NEWS = 4;
    PRODUCTS = 5;
    VIDEO = 6;
  }
  Corpus corpus = 4; // 定义参数 corpus 的类型为枚举类型 Corpus，分配字段编号为 4。
}
```

枚举类型别名示例

```
enum EnumAllowingAlias {
  option allow_alias = true; // 允许枚举类型 EnumAllowingAlias 出现别名（字段值编号相同）。
  UNKNOWN = 0;
  STARTED = 1;
  RUNNING = 1;
}

enum EnumNotAllowingAlias {
  UNKNOWN = 0;
  STARTED = 1;
  // RUNNING = 1;  // 取消注释后，源代码生产工具将会发生编译错误，并提示错误信息。
}
```

消息导入示例

```
import "myproject/other_protos.proto";
```

嵌套消息类型定义示例

```
message SearchResponse {
  message Result {
    string url = 1;
    string title = 2;
    repeated string snippets = 3;
  }
  repeated Result results = 1;
}
```

任意值类型示例

```
import "google/protobuf/any.proto";

message ErrorStatus {
  string message = 1;
  repeated google.protobuf.Any details = 2;
}
```

Oneof 示例

```
message SampleMessage {
  oneof test_oneof {
    string name = 4;
    SubMessage sub_message = 9;
  }
}
```

指定 json_name 示例

```
// 第三方 app 登录响应
message OtherAppLoginResponse {
    Response response = 1; // 接口响应信息
    int32 user_id = 2 [json_name="user_id"]; // 用户 id，json 输出为 user_id
    string access_token = 3; // 登录 token，json 输出为 accessToken
    string token_type = 4; // 刷新 token，json 输出为 tokenType
    string expire_at = 5; // 失效时间，json 输出为 expireAt
}
```


### 3. 附录

协议缓冲区语言指引第三版：https://developers.google.cn/protocol-buffers/docs/proto3

[协议缓冲区语言指引第三版]: https://developers.google.cn/protocol-buffers/docs/proto3