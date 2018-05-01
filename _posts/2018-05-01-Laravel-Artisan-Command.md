---
layout: post
title: "Laravel中Artisan命令及说明"
date: 2018-05-01 11:03:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- PHP
- Laravel
- Artisan
meta:
  _edit_last: '1'
---

Artisan是PHP框架Laravel中提供的命令行接口（CLI），它提供了一些功能来辅助开发者开发Laravel应用。它可以通过命令行的方式生成App Key，还可以生成代码中的Controller。

下面具体列出所有可用的Artisan命令。

| 命令      |  说明      
| --------  | ------- 
| php artisan key:generate      | 生成App Key    
| php artisan make:controller   | 生成controller控制器  
| php artisan make:model        | 生成model模型 
| php artisan make:policy       | 生成授权策略  
| php artisan make:seeder       | 生成seed文件  
| php artisan migrate           | 迁移  
| php artisan migrate:rollback  | 迁移回滚  
| php artisan migrate:refresh   | 重置  
| php artisan db:seed           | 填充数据库  
| php artisan tinker            | 进入tinker环境   
| php artisan route:list        | 查看路由

还可以使用help来查看Artisan指令的使用说明：

`php artisan help make`  
  





