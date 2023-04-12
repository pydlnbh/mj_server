# 威海麻将后端

----

## 有言在先
该项目只是一个**教学研究项目，不具备某些特殊功能！**

客户端地址请访问：https://github.com/hjj2017/whmj.cocos2d_client

威海地方玩法麻将，Java 服务端代码，JDK 语言版本 OpenJDK 13+33。
想要在 IntelliJ IDEA 中成功运行代码，只需要依次启动两个服务器：

- proxyserver
- bizserver

在启动这两个服务器之前，当然还需要做一些准备工作。

## 初始化 MySQL 数据库
需要建立 mj_game、mj_log、mj_log_template 这三个数据库。

```
create database mj_game         default character set utf8mb4;
create database mj_log          default character set utf8mb4;
create database mj_log_template default character set utf8mb4;
```

建立数据库完成之后，需要导入相应的数据库文件：

```
use mj_game
source etc/sql/mj_game.sql;                -- 创建 t_user、t_club、t_club_member 等数据表
source etc/sql/mj_cost_room_card_conf.sql; -- 导入房卡消耗数量配置

use mj_log_template
source etc/sql/mj_log_temlate.sql;         -- 导入日志记录模板表
```

## 初始化 Redis
MySQL 数据库初始化完成之后，还需要初始化 Redis，主要是建立用户 Id 池和老友圈 Id 池。
这需要你的机器有 Python3 环境！
并且已经为 Python3 安装了 pymysql 和 redis 扩展。

如果你已经安装好 Python3 及其扩展，那么运行以下命令即可：

```
python3 etc/tool/gen_user_id.py
python3 etc/tool/gen_club_id.py
```

**注意，在运行前需要确保 .py 文件中配置的数据库地址、用户名和密码是否正确。**

执行无误的话，会输出如下内容：

```
>>> 从 MySQL 中读取用户 Id 列表 <<<
共加载 0 条用户 Id
>>> 写入用户 Id 到 Redis <<<
+++ 全部完成 +++

>>> 从 MySQL 中读取亲友圈 Id 列表 <<<
共加载 0 条亲友圈 Id
>>> 写入亲友圈 Id 到 Redis <<<
+++ 全部完成 +++
```

## 启动 proxyserver
启动 proxyserver 时，需要在 IDEA 中添加以下参数：

```
--server_id=1001
--server_name=proxy_server_1001
-h 0.0.0.0
-p 20480
-c ../etc/proxyserver_all.conf.json
```

**注意：还需要修改工作目录为 whmj.java_server\proxyserver**

## 启动 bizserver
启动 proxyserver 时，需要在 IDEA 中添加以下参数：

```
--server_id=2001
--server_name=biz_server_2001
--server_job_type_set=PASSPORT,HALL,GAME,CLUB,CHAT,RECORD
-h 127.0.0.1
-p 40960
-c ../etc/bizserver_all.conf.json
```

**注意：还需要修改工作目录为 whmj.java_server\bizserver**

如果希望 bizserver 工作在测试模式中，那么需要增加环境变量：
MJ_WORK_MODE=DEV，重启服务器就可以使用测试用户登录游戏。

## 怎样测试服务器
可以打开本机浏览器，然后输入以下地址：

http://cdn0001.afrxvk.cn/whmj/go.html?serverAddr=127.0.0.1:20480

这样，麻将客户端就会尝试连接本地服务器。

如果是工作在测试模式中，可以使用下面这个地址：

http://cdn0001.afrxvk.cn/whmj/go.html?serverAddr=127.0.0.1:20480&DEV=1&testerName=测试用户名称

祝你好运！
