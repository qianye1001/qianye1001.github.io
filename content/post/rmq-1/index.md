---
title: Netty 与 零拷贝
description: RocketMQ的学习记录
slug: rmq-netty
date: 2023-6-18 12:00:00+0000
image: cover.jpg
categories:
    - 消息队列
tags:
    - RocketMQ
    - Netty
---

# Netty 与 零拷贝

## 前言
在学习RocketMQ的过程中发现其零拷贝功能并不推荐开启，但是Kafka的零拷贝能力为其提供了较大的吞吐能力，为什么在RocketMQ中却不推荐开启呢？对比发现RocketMQ使用了Netty网络库的零拷贝，而Kafka的零拷贝是自己实现的，通过学习Netty的线程池和网络模型，可以从中发现问题。

## 
