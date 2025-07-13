# ubuntu上docker的搭建

[Vulhub - 开源的漏洞Docker环境](https://vulhub.org/zh)

点击上面的网站

在网站最下面找到开始使用，有搭建的命令

：1<img width="1274" height="683" alt="1" src="https://github.com/user-attachments/assets/dc1052e6-c075-4b6c-b036-ce87549234ee" />


//安装docker，连接失败多试几次

curl -fsSL https://get.docker.com | sh

//验证Docker是否正确安装：

docker version

//还要验证Docker Compose是否可用：

docker compose version





安装挂载proxychains：

apt install proxychains

编辑配置文件：

 vim /etc/proxychains.conf 

对最下面的配置进行修改：

socks4  192.168.163.1 7897

​                网关ip              端口



win+r输入cmd：输入ipconfig查看ip以太网适配器 VMware Network Adapter VMnet1或8

：2<img width="744" height="388" alt="2" src="https://github.com/user-attachments/assets/24b59fb0-9b16-4a41-8e8a-b7a919f77317" />


proxychains curl http://www.google.com

测试是否可以成功访问谷歌



打开你的梯子在设置里查看端口：3<img width="668" height="525" alt="3" src="https://github.com/user-attachments/assets/1245142d-34f3-4f12-93a3-3450fdc7fbf0" />






点击开头github点击code复制里面的链接进行克隆:https://github.com/vulhub/vulhub.git

proxychains git clone --depth 1 https://github.com/vulhub/vulhub.git





切换目录：选择漏洞环境 浏览仓库并选择您想要探索的漏洞。每个目录代表一个不同的漏洞应用程序。

cd vulhub/cacti/CVE-2022-46169/



启动环境 使用Docker Compose构建并启动漏洞环境

docker compose up -d





拉镜像是遇到了报错Get "https://registry-1.docker.io/v2/": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers) 大概就是请求超时，是网络问题导致镜像没拉下来

配置一个加速地址

vim /etc/docker/daemon.json

添加下面内容
{
	"registry-mirrors": [
	 				"https://docker.211678.top",
					"https://docker.1panel.live",
					"https://hub.rat.dev",
					"https://docker.m.daocloud.io",
					"https://do.nark.eu.org",
					"https://dockerpull.com",
					"https://dockerproxy.cn",
					"https://docker.awsl9527.cn"
	]
}

然后保存 重启

systemctl daemon-reload
systemctl restart  docker







vscode上dockers连接失败解决方法：

先在商店中下载docker

如果安装后连接失败可能是当前用户没有权限导致的：

需要将普通用户加入到 docker 用户组，并重启，即可不使用 sudo 来运行 docker 相关操作

sudo usermod -aG docker $USER



重启：

reboot

就可以成功连接：

4<img width="1280" height="764" alt="4" src="https://github.com/user-attachments/assets/b74e69e2-851d-4627-88d6-2c40e6d5aab5" />












# ubuntu上nginx和php：

1.安装依赖包

apt-get install gcc
apt-get install libpcre3 libpcre3-dev
apt-get install zlib1g zlib1g-dev
sudo apt-get install openssl 
sudo apt-get install libssl-dev



2.安装nginx



cd /usr/local

mkdir nginx

cd nginx

wget http://nginx.org/download/nginx-1.21.6.tar.gz

tar -xvf nginx-1.21.6.tar.gz



3.编译nginx



cd /usr/local/nginx/nginx-1.21.6

执行命令

./configure
安装make

apt install make 
     执行make命令 
make

执行make install命令

make install4





4.启动nginx

cd /usr/local/nginx/sbin



启动nginx



./nginx







5.访问nginx

在浏览器上输入ubuntu虚拟机的ip

：5<img width="1280" height="770" alt="5" src="https://github.com/user-attachments/assets/9e622546-4a20-40f4-8e68-2f7de49bdbe4" />








7.安装php

​    nginx使用php的话要用到php7.3-fpm,所以要安装

sudo apt-get install php7.3 php7.3-mysql php7.3-fpm php7.3-curl php7.3-xml php7.3-gd php7.3-mbstring php-memcached php7.3-zip



8.配置php-fpm

​    把监听端口改掉

把上面一行的换成下面那个

;listen = /run/php/php7.3-fpm.sock
listen = 127.0.0.1:9000



9.启动php-fpm

sudo service php7.3-fpm start
sudo service php7.3-fpm restart
netstat -lnt | grep 9000

查看9000端口

：6<img width="1280" height="73" alt="6" src="https://github.com/user-attachments/assets/ea0ac10b-c23f-4f82-85ed-91e021261da0" />










# https和dns：





### DNS域名解析过程

首先，客户端先在本地缓存查找有没有域名缓存，如果没有，客户端发出DNS请求翻译IP地址或主机名。DNS服务器在收到客户机的请求后：

（1）检查DNS服务器的缓存，若查到请求的地址或名字，即向客户机发出应答信息；

（2）若没有查到，则在数据库中查找，若查到请求的地址或名字，即向客户机发出应答信息；

（3）若没有查到，则将请求发给根域DNS服务器，并依序从根域查找顶级域，由顶级查找二级域，二级域查找三级，直至找到要解析的地址或名字，即向客户机所在网络的DNS服务器发出应答信息，DNS服务器收到应答后现在缓存中存储，然后，将解析结果发给客户机。

（4）若没有找到，则返回错误信息





### https原理--RSA密钥协商算法



##### TLS 第一次握手

客户端首先会发一个「**Client Hello**」消息，字面意思我们也能理解到，这是跟服务器「打招呼」。

消息里面有客户端使用的 TLS 版本号、支持的密码套件列表，支持的压缩算法，以及生成的**随机数（\*Client Random\*）**，这个随机数会被服务端保留，它是生成对称加密密钥的材料之一。



##### TLS 第二次握手

当服务端收到客户端的「Client Hello」消息后，会确认 TLS 版本号是否支持，和从密码套件列表中选择一个密码套件，还有选择压缩算法（安全性原因，一般不压缩），以及生成**随机数（\*Server Random\*）**。

接着，返回「**Server Hello**」消息，消息里面有服务器确认的 TLS 版本号，也给出了随机数（Server Random），然后从客户端的密码套件列表选择了一个合适的密码套件。

前面这两个客户端和服务端相互「打招呼」的过程，客户端和服务端就已确认了 TLS 版本和使用的密码套件，而且你可能发现客户端和服务端都会各自生成一个随机数，并且还会把随机数传递给对方。

这两个随机数是后续作为生成「会话密钥」的条件，所谓的会话密钥就是数据传输时，所使用的对称加密密钥。

然后，服务端为了证明自己的身份，会发送「**Server Certificate**」给客户端，这个消息里含有数字证书。

随后，服务端发了「**Server Hello Done**」消息，目的是告诉客户端，我已经把该给你的东西都给你了，本次打招呼完毕。、

##### TLS第三次握手

客户端验证完证书后，认为可信则继续往下走。接着，客户端就会生成一个新的**随机数 (pre-master)**，用服务器的 RSA 公钥加密该随机数，通过「**Change Cipher Key Exchange**」消息传给服务端。

服务端收到后，用 RSA 私钥解密，得到客户端发来的随机数 (pre-master)。

至此，**客户端和服务端双方都共享了三个随机数，分别是 Client Random、Server Random、pre-master**。

于是，双方根据已经得到的三个随机数，生成**会话密钥（Master Secret）**，它是对称密钥，用于对后续的 HTTP 请求/响应的数据加解密。

生成完会话密钥后，然后客户端发一个「**Change Cipher Spec**」，告诉服务端开始使用加密方式发送消息。

然后，客户端再发一个「**Encrypted Handshake Message（Finishd）**」消息，把之前所有发送的数据做个摘要，再用会话密钥（master secret）加密一下，让服务器做个验证，验证加密通信是否可用和之前握手信息是否有被中途篡改过。

可以发现，「Change Cipher Spec」之前传输的 TLS 握手数据都是明文，之后都是对称密钥加密的密文。

##### TLS 第四次握手

服务器也是同样的操作，发「**Change Cipher Spec**」和「**Encrypted Handshake Message**」消息，如果双方都验证加密和解密没问题，那么握手正式完成。

最后，就用「会话密钥」加解密 HTTP 请求和响应了。











