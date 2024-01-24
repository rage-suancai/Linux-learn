## 环境安装和项目部署
在学习完了Linux操作系统的一些基本操作之后 我们接着来看如何进行项目的环境安装和部署 包括安装JDK, Nginx服务器 以及上传我们的SpringBoot项目并运行

我们可以直接使用apt进行软件的安装 它是一个高级的安装包管理工具 我们可以直接寻找对应的软件进行安装 无需再去官网进行下载
非常方便 软件仓库中默认已经帮助我们存放了大量实用软件的安装包 只需要一个安装命令就可以进行安装了

实际上Ubuntu系统已经为我们自带了一些环境了 比如Python3:

                        test@ubuntu-server:~$ python3
                        Python 3.8.10 (default, Nov 26 2021, 20:14:08)
                        [GCC 9.3.0] on linux
                        Type "help", "copyright", "credits" or "license" for more information.
                        >>> print("HelloWorld！")
                        HelloWorld！
                        >>> exit()

C语言的编译工具GCC可以通过APT进行安装:

```shell
                        sudo apt install gcc
```

安装后 可以编写一个简单的C语言程序并且编译为可执行文件:

```c
                        #include<stdio.h>

                        int main(){
                                printf("Hello World!\n");
                        }
```

```shell
                        test@ubuntu-server:~$ vim hello.c
                        test@ubuntu-server:~$ gcc hello.c -o hello
                        test@ubuntu-server:~$ ./hello 
                        Hello World!
```

而JDK实际上安装也非常简单 通过APT即可:

```shell
                        test@ubuntu-server:~$ sudo apt install openjdk-8-j
                        openjdk-8-jdk           openjdk-8-jre           openjdk-8-jre-zero      
                        openjdk-8-jdk-headless  openjdk-8-jre-headless  
                        test@ubuntu-server:~$ sudo apt install openjdk-8-jdk
```

接着我们来测试一下编译和运行 首先编写一个Java程序:

```shell
                        test@ubuntu-server:~$ vim Main.java
```

```java
                        public class Main {
                                public static void main(String[] args){
                                        System.out.println("Hello World！");
                                }
                        }
```

```shell
                        test@ubuntu-server:~$ javac Main.java 
                        test@ubuntu-server:~$ ls
                        Main.class  Main.java
                        test@ubuntu-server:~$ java Main 
                        Hello World！
```

接着我们来部署一下Redis服务器:

```shell
                        test@ubuntu-server:~$ sudo apt install redis
```

安装完成后 可以直接使用`redis-cli`命令打开Redis客户端连接本地的服务器:

```shell
                        test@ubuntu-server:~$ redis-cli
                        127.0.0.1:6379> keys *
                        (empty list or set)
```

使用和之前Windows下没有区别

接着我们安装一下MySQL服务器 同样的 直接使用apt即可:

```shell
                        sudo apt install mysql-server-8.0
```

我们直接直接登录MySQL服务器 注意要在root权限下使用 这样就不用输入密码了:

```shell
                        sudo mysql -u root -p
                        Enter password: 
                        Welcome to the MySQL monitor.  Commands end with ; or \g.
                        Your MySQL connection id is 11
                        Server version: 8.0.27-0ubuntu0.20.04.1 (Ubuntu)
                        
                        Copyright (c) 2000, 2021, Oracle and/or its affiliates.
                        
                        Oracle is a registered trademark of Oracle Corporation and/or its
                        affiliates. Other names may be trademarks of their respective
                        owners.
                        
                        Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
                        
                        mysql> exit
```

可以发现实际上就是我们之前在Windows的CMD中使用的样子 接着我们就创建一个生产环境下使用的数据库:

```shell
                        mysql> create database book_manage;
                        mysql> show databases;
                        +--------------------+
                        | Database           |
                        +--------------------+
                        | book_manage        |
                        | information_schema |
                        | mysql              |
                        | performance_schema |
                        | sys                |
                        +--------------------+
                        5 rows in set (0.01 sec)
```

接着我们创建一个用户来使用这个数据 一会我们就可以将SpringBoot配置文件进行修改并直接放到此服务器上进行部署

```shell
                        mysql> create user test identified by '123456';
                        Query OK, 0 rows affected (0.01 sec)
                        
                        mysql> grant all on book_manage.* to test;
                        Query OK, 0 rows affected (0.00 sec)
```

如果觉得这样很麻烦不是可视化的 可以使用Navicat连接进行操作 注意开启一下MySQL的外网访问

```shell
                        test@ubuntu-server:~$ mysql -u test -p
                        Enter password: 
                        Welcome to the MySQL monitor.  Commands end with ; or \g.
                        Your MySQL connection id is 13
                        Server version: 8.0.27-0ubuntu0.20.04.1 (Ubuntu)
                        
                        Copyright (c) 2000, 2021, Oracle and/or its affiliates.
                        
                        Oracle is a registered trademark of Oracle Corporation and/or its
                        affiliates. Other names may be trademarks of their respective
                        owners.
                        
                        Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
                        
                        mysql> show databases;
                        +--------------------+
                        | Database           |
                        +--------------------+
                        | book_manage        |
                        | information_schema |
                        +--------------------+
                        2 rows in set (0.01 sec)
```

使用test用户登录之后 查看数据库列表 有book_manage就OK了

最后我们修改一下SpringBoot项目的生产环境配置即可:

```shell
                        spring:
                        
                          mail:
                            host: smtp.163.com
                            username: javastudy111@163.com
                            password: TKPGLAPDSWKGJOWK
                            
                          datasource:
                            url: jdbc:mysql://localhost:3306/book_manage
                            driver-class-name: com.mysql.cj.jdbc.Driver
                            username: test
                            password: 123456
                            
                          jpa:
                            show-sql: false
                            hibernate:
                              ddl-auto: update
                              
                        springfox:
                          documentation:
                            enabled: false
```

然后启动我们的项目:

```shell
                        test@ubuntu-server:~$ java -jar springboot-project-0.0.1-SNAPSHOT.jar
```

现在我们将前端页面的API访问地址修改为我们的SpringBoot服务器地址 即可正常使用了

我们也可以将我们的静态资源使用Nginx服务器进行代理:

    Nginx("engine x")是一款是由俄罗斯的程序设计师IgorSysoev所开发高性能的Web和反向代理服务器
    也是一个IMAP/POP3/SMTP代理服务器 在高连接并发的情况下 Nginx是Apache服务器不错的替代品

Nginx非常强大 它能够通提供非常方便的反向代理服务 并且支持负载均衡 不过我们这里用一下反向代理就可以了 实际上就是代理我们的前端页面 然后我们访问Nginx服务器即可访问到静态资源
这样我们前后端都放在了服务器上(你也可以搞两台服务器 一台挂静态资源一台挂SpringBoot服务器 实现真正意义上的分离 有条件的还能上个域名和证书啥的)

安装如下:

```shell
                        test@ubuntu-server:~$ sudo apt install nginx
```

安装完成后 我们可以直接访问: http://192.168.10.4/ 能够出现Nginx页面表示安装成功

接着我们将静态资源上传到Linux服务器中 然后对Nginx进行反向代理配置:

```shell
                        test@ubuntu-server:~$ cd /etc/nginx/
                        test@ubuntu-server:/etc/nginx$ ls
                        conf.d		koi-utf     modules-available  proxy_params	sites-enabled  win-utf
                        fastcgi.conf	koi-win     modules-enabled    scgi_params	snippets
                        fastcgi_params	mime.types  nginx.conf	       sites-available	uwsgi_params
                        test@ubuntu-server:/etc/nginx$ sudo vim nginx.conf
```

                        server {
                                        listen       80;
                                        server_name  192.168.10.4;
                                        add_header Access-Control-Allow-Origin *;
                                        location / {
                                                root /home/test/static;
                                                charset utf-8;
                                                add_header 'Access-Control-Allow-Origin' '*';
                                                add_header 'Access-Control-Allow-Credentials' 'true';
                                                add_header 'Access-Control-Allow-Methods' '*';
                                                add_header 'Access-Control-Allow-Headers' 'Content-Type,*';
                                        }
                                }

然后就可以直接访问到我们的前端页面了 这时再开启SpringBoot服务器即可 可以在最后添加&符号表示后台启动