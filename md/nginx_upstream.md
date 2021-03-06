### centos安装nginx 带upstream

用途:利用upstream进行socket数据中转  
各版本nginx下载地址：http://nginx.org/download/  
系统：CentOS 6.5 x64  
nginx版本：1.12.1  
安装方式：源码编译安装  

1.安装必须环境,nginx的编译需要c++，同时prce（重定向支持）和openssl（https支持）也需要安装  
```
yum install gcc-c++
yum -y install pcre*
yum -y install openssl*
```

2.下载nginx-1.12.1.tar.gz，放在 /usr/local/ 目录下  
```
cd /usr/local/
wget http://nginx.org/download/nginx-1.12.1.tar.gz
tar zxf nginx-1.12.1.tar.gz
cd nginx-1.12.1
./configure --prefix=/usr/local/nginx --with-stream
make && make install
```
参考:https://www.cnblogs.com/chenjianxiang/p/8489055.html  

3.打开防火墙需要允许访问的端口，如端口80，或者直接关闭防火墙  
```
[root@localhost ~]#servcie iptables stop     //临时关闭防火墙
[root@localhost ~]#chkconfig iptables off    //永久关闭防火墙
```
注:如果提示service not found,下面三种方式解决  
a:yum install initscripts -y  
b:直接使用su - root来切换到root用户，然后使用 service   
c:使用su root切换到root用户，并同时使用/sbin/service来操作,/sbin/service iptables stop
 
4.nginx.conf配置文件如下  
```
user  nobody;
worker_processes  1;

error_log  logs/error.log;
error_log  logs/error.log  notic/sbine;
error_log  logs/error.log  info;

pid        logs/nginx.pid;

events {
    worker_connections  1024;
}

stream {
    upstream upstream_8002 {
        server us-free.hyss.xyz:48528;  
    }
    server {
        listen 8002;
        listen 8002 udp;
        proxy_pass upstream_8002;
        proxy_timeout 10s;
        proxy_connect_timeout 10s;   
    }

}

```

5.进入/usr/local/nginx 启动nginx:./nginx  

6.如果端口被占用  

&emsp;&emsp;&emsp;查找端口占用  
&emsp;&emsp;&emsp;netstat -lnp|grep xxx   xxx请换为你的nginx需要的端口，如：80,会提示进程多少如:1777  
&emsp;&emsp;&emsp;ps 1777 可以看到是哪个路径  
&emsp;&emsp;&emsp;kill -9 1777 //杀掉编号为1777的进程

7.nginx启动报错  

&emsp;&emsp;&emsp;/usr/local/nginx/logs/nginx.pid  路径下找不到nginx.pid   
&emsp;&emsp;&emsp;执行:/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf



8:centos7 设置开机启动
vim /etc/rc.d/rc.local

增加内容

/usr/local/nginx/sbin/nginx -c /nginx/nginx-1.15.10/conf/nginx.conf

注意 -c 之后的 文件..  需要确定.

9:centos7关闭防火墙
1、firewalld的基本使用

启动： systemctl start firewalld

关闭： systemctl stop firewalld

查看状态： systemctl status firewalld 

开机禁用  ： systemctl disable firewalld

开机启用  ： systemctl enable firewalld
