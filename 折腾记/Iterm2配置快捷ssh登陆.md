### 说明

- iterm2啥都好，但是就是没有一个类似deepin的雷神客户端的便捷ssh服务器连接工具。

- 但是，它有扩展功能可以执行脚本啊

- 所以写一个扩展脚本就好啦



1、配置
打开iTerm2，（快捷键command + o）打开Profiles，在General标签下修改Command为Command，里面直接写登录的信息：

方法A
ssh -i server.pem ubuntu@ip
方法B
expect /Users/oom/shell脚本路径

然后写一个脚本
```shell script
#!/usr/bin/expect

set PORT 22
set HOST 100.100.100.100
set USER root
set PASSWORD 123456

spawn ssh -p $PORT $USER@$HOST
expect {
        "yes/no" {send "yes\r";exp_continue;}
         "*password:*" { send "$PASSWORD\r" }
        }
interact
```




2、使用
打开iTerm2，（快捷键command + o）打开配置的Profiles即可。



### 完美

