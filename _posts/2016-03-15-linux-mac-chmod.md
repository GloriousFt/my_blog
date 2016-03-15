---
layout: post
title: chmod权限指令记录
date: 2016-03-15 13:50:27.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- chmod
meta:
  _edit_last: '1'
---
经常用chmod来更改文件权限,有时会忘记部分权限累加的含义,这里记录一下数字模式
```bash
chmod 400 file - Read by owner
chmod 040 file - Read by group
chmod 004 file - Read by world

chmod 200 file - Write by owner
chmod 020 file - Write by group
chmod 002 file - Write by world

chmod 100 file - execute by owner
chmod 010 file - execute by group
chmod 001 file - execute by world
```