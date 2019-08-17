---
title: Protocol Buffers 简介
date: 2019-08-17 23:59:58
categories: 开发
tags: Protocol-Buffers
english_title: Introduction-to-Protocol-Buffers
---

# Protocol Buffers 简介

## 1. 什么是 Protocol Buffers?

Protocol Buffers (ProtocolBuffer/protobuf) 是 Google 公司开发的一种数据描述语言.

## 2. 为什么使用 Protocol Buffers?

Protocol Buffers 是类似于 XML、JSON 的一种可扩展性描述语言，它可用于数据储存、协议通讯等多方面。和 XML、JSON 相比，它将数据结构序列化成二进制（可选序列化成 JSON 格式），在序列化时，去除了对无用信息的储存。所以它更小、更快、更简单。我们只需要定义一次数据结构协议，然后可以使用工具生成不同语言之间操作读取和序列化数据结构的源代码。它可以让我们更方便的在不同语言、不同的数据流之前进行操作。

## 3. 语言支持情况

于今日今时（2019-08-17 16:37），Protocol Buffers 协议第二版已支持生成 Java, Python, Objective-C, C++ 等语言的源代码。在第三版协议中，新增了对 Dart, Go, Ruby, C# 等语言的源代码生成支持（更多语言详见参考网页）。以后也将陆陆续续地新增对其他语言的支持。

## 附录

协议引导概览: https://developers.google.cn/protocol-buffers/docs/overview
参考网页：https://developers.google.cn/protocol-buffers/docs/reference/overview