## grep命令介绍
grep 查找文件里符合条件的字符串，常与管道符 |、cat、ps一起使用；

主要用于查找文件中符合条件的字符串、统计文件中符合条件的字符串行数、grep 不显示自身进程

## 如何查看进程信息？
ps

ps -ef | grep redis

-e: 显示所有进程

-f: 全格式

这样 grep 命令就可以在完整的信息中进行搜索

## linux查询日志中指定字段？
用 grep 命令，后面加关键字，然后再加文件的名称就行了。

grep yes /etc/redis.conf

或者这样也行，配合管道符 |

cat /etc/redis.conf | grep yes

## 如何查看linux cpu内存？
top

free

## 怎么用命令将一个文件传输到服务器上？
scp 命令

## 改变文件权限












