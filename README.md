# go-id-builder
[![travis-ci](https://travis-ci.org/freshcn/go-id-builder.svg?branch=master)](https://travis-ci.org/freshcn/go-id-builder)

这是一个使用golang开发的ID生成器，它可以提供通过高效的方式产生连续唯一的ID值。在分库分表时可以提供非常有用的帮助。
# 为什么要做id生成器

常常在数据库进行分库分表的需求处理时，需要给表产生一个自增的主键id。单表的时候我们都是通过给表添加一个自增字段来实现的。当需要分表时就会发现这样的方式会出现每个表都有一套自己的自增id。特别是我们需要通过这个ID来实现分表算法时（一般都是id%表数量），那么如何在多表中产生一个连续自增的ID成为一个问题。

# 如何实现的

go-id-builder使用mysql来做为最大id数的持久化存储。程序在每次启动的时候都会加载数据表中当前的所记录的id类型，将会自动申请1000个（配置文件中可修改）新的id号，加载到一个缓冲通道中，当用户向生成器的api接口发起请求时，从对应的缓冲通道中将数据取出返回给客户端。

# 安装方式

你可以直接通过下面的命令来直接安装

`go get github.com/freshcn/go-id-builder`

将 `db.sql`中的数据表结构导入到你的 MYSQL 数据库中，在 app.ini 中配置你的mysql 数据的连接信息。

也可以通过项目的`releases` https://github.com/freshcn/go-id-builder/releases 中直接下载二进制包来安装运行

# 配置数据库

先将`db.sql`中的mysql语句安装到数据库中，并在`app.ini`中配置相应的mysql的数据库连接信息。数据库脚本中默认为表设置的是innodb引擎，你可以按需要修改为你正在使用的其他引擎。

数据库中已经默认提供了一个test   id名，你可以在运行了程序后通过

`http://localhost:3002?name=test`

来获取到test这个id名所产生的第一个id值。

你可以向数据表中添加新的id名来支持新的id值的产生

# api说明

在程序运行成功后默认会占用系统的3002端口，你可以在配置文件中修改所占用的端口号。

你可以通过get或post请求方式来请求接口。允许的参数如下表：

- name - id值名
- num - 需要请求的id数量，默认最大为100个，可在配置文件中修改
