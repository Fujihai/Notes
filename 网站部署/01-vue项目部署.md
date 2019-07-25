# 云服务器部署 Vue 项目

## 云服务器登录

推荐一款远程登录软件：Xshell。

### 步骤

使用  **账号密码** 的方式登录。

* 创建连接。公网 IP : **49.232.35.54**，端口号使用默认的 **22** 端口，协议选择 **SSH**。
* 接收并保存 **主机密钥指纹**。
* 登录账号：默认是 root 用户，密码自设。

## 上传文件

使用 **[WinSCP]([https://winscp.net/eng/docs/lang:chs#%E4%B8%8B%E8%BD%BD](https://winscp.net/eng/docs/lang:chs#下载))**  进行文件上传

### 参考

[Windows 机器通过 WinSCP 上传/下载文件](<https://cloud.tencent.com/document/product/213/2131>)。

## 服务端部署

### 服务端

服务端需要安装 nginx 。

#### 使用 yum 方式进行安装

* 安装 `nginx` : `yum -y install nginx`

* 启动

  ```shell
  sudo systemctl start nginx.service
  sudo systemctl enable nginx.service
  ```

* 输入 **服务器公网 IP** 或者 **绑定好的域名**，若看到测试页，则代表 `nginx` 安装成功。

### Vue 项目打包

使用 `npm run build` 将项目打包成 `dist` 文件夹，并将内容上传至服务器。

### 服务端 `nginx` 配置

使用命令 `find / -name nginx.conf` 查找 `nginx` 配置文件。

#### 默认的 `nginx.conf` 配置内容：

```javascript
server {
    listen       80 default_server;
    listen       [::]:80 default_server;
    server_name  _;
    root         /usr/share/nginx/html;

    # Load configuration files for the default server block.
    include /etc/nginx/default.d/*.conf;

    location / {
    }

    error_page 404 /404.html;
       location = /40x.html {
    }

	error_page 500 502 503 504 /50x.html;
       location = /50x.html {
    }
}
```

#### 修改后的配置内容

```javascript
server {
	listen       80 default_server;
    listen       [::]:80 default_server;
    # server_name  _;
    server_name  49.232.35.54;
    # root         /usr/share/nginx/html;
    root         /root/home/leif/vue-tmall;
    index index.html;

    # Load configuration files for the default server block.
    include /etc/nginx/default.d/*.conf;

    location / {
       root /root/home/leif/vue-tmall;
       index index.html;
    }

	error_page 404 /404.html;
       location = /40x.html {
    }

	error_page 500 502 503 504 /50x.html;
       location = /50x.html {
    }
}
```

> 配置说明：
>
> line 2 && 3 : 配置服务器监听的端口，默认 80 端口。
>
> line 5 : server_name 配置的是 **服务器的 IP**  或者 **域名** 。
>
> line 7 : vue 项目 index.html 在服务器上的外层目录，注意是从 **根目录**开始。
>
> line 8 : 默认是 index.html 文件。
>
> line 13 ~ 14 : 设置 访问网址后跳转的路径。

#### 测试 nginx 配置文件是否正确

进入 `nginx` 安装目录 `/usr/sbin` 下，输入命令 `./nginx -t`。

```javascript
[root@VM_0_13_centos sbin]# ./nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

> 出现以上现象，证明 **配置文件语法** 没有错误，但是不代表服务器就可以正常访问了。

#### 重启 nginx 服务

 修改完配置文件后，在`nginx` 安装目录 `/usr/sbin` 下，重启 `nginx` 服务。

```javascript
./nginx -s reload
```

#### 403 Forbidden 错误

* 查看 `nginx` 日志文件 `error.log` 定位问题，日志文件路径 `/var/log/nginx/error.log`，有些系统不一定是此路径。

* 日志内容如下：

  ```javascript
  2019/07/19 10:19:16 [error] 25157#0: *84 "/root/home/leif/vue-tmall/index.html" is forbidden (13: Permission denied), client: 113.116.236.188, server: 49.232.35.54, request: "GET / HTTP/1.1", host: "49.232.35.54"
  ```

* 此问题与 **权限** 相关，查看 `nginx` 进程相关信息，命令 ： `ps -ef | grep nginx` 。

  ```javascript
  [root@VM_0_13_centos sbin]# ps -ef | grep nginx
  root     22759     1  0 Jul18 ?        00:00:00 nginx: master process /usr/sbin/nginx
  nginx    25157 22759  0 10:18 ?        00:00:00 nginx: worker process
  root     25977 23124  0 10:25 pts/0    00:00:00 grep --color=auto nginx
  ```

  > nginx 的进程 worker process 用户是 nginx，而 master process 的用户是 root。通过配置文件将 worker process 的进程 user 改成 root 即可。
  
  ```javascript
  user nginx; -> user root;
  ```

* 重新加载配置文件 `./nginx -s reload`，查看 `nginx` 进程相关信息 `ps -ef | grep nginx` 。

  ```javascript
  [root@VM_0_13_centos sbin]# ps -ef | grep nginx
  root     22759     1  0 Jul18 ?        00:00:00 nginx: master process /usr/sbin/nginx
  root     26655 22759  0 10:32 ?        00:00:00 nginx: worker process
  root     26657 23124  0 10:32 pts/0    00:00:00 grep --color=auto nginx
  ```

  > master process 与 worker process 的进程用户均为 root。

* 重新访问网站，问题解决。
