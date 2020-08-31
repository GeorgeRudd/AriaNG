# AriaNG
AriaNG服务器安装并使用Https/WebSocket(安全)协议连接Aria2

## 安装caddy
```
wget --no-check-certificate -O caddy_install.sh https://raw.githubusercontent.com/xyh101/doubi/master/caddy_install.sh
chmod +x caddy_install.sh
./caddy_install.sh

启动：/etc/init.d/caddy start
停止：/etc/init.d/caddy stop
重启：/etc/init.d/caddy restart
查看状态：/etc/init.d/caddy status
查看Caddy启动日志：tail -f /tmp/caddy.log
安装目录：/usr/local/caddy
Caddy配置文件位置：/usr/local/caddy/Caddyfile
Caddy自动申请SSL证书位置：/root/.caddy/acme/acme-v01.api.letsencrypt.org/sites/xxx.xxx(域名)/
```
* caddyfile的写法,可以使用https访问面板
```
#位置/usr/local/caddy/Caddyfile
#https访问，该配置会自动签发SSL，请提前解析域名到VPS服务器
echo "wdwp.tk {
 gzip
 tls gcphym1@gmail.com
 proxy / 127.0.0.1:1234 {
    header_upstream Host {host}
    header_upstream X-Real-IP {remote}
    header_upstream X-Forwarded-For {remote}
    header_upstream X-Forwarded-Port {server_port}
    header_upstream X-Forwarded-Proto {scheme}
  }
}

xz.wdwp.tk {
 gzip
 tls gcphym1@gmail.com
 root /usr/local/caddy/www/aria2
 browse
}

qb.wdwp.tk {
 gzip
 tls gcphym1@gmail.com
 proxy / 127.0.0.1:2017 {
    header_upstream Host {host}
    header_upstream X-Real-IP {remote}
    header_upstream X-Forwarded-For {remote}
    header_upstream X-Forwarded-Port {server_port}
    header_upstream X-Forwarded-Proto {scheme}
  }
}" > /usr/local/caddy/Caddyfile
```

## 安装AriaNG
* 安装Caddy成功后，继续新建一个虚拟主机文件夹
```
mkdir /usr/local/caddy/www && mkdir /usr/local/caddy/www/aria2
```
* 安装解压缩依赖
```
# CentOS 系统：
yum install unzip -y
# Debian/Ubuntu 系统：
apt-get install unzip -y
```
```
# 进入文件夹 #
cd /usr/local/caddy/www/aria2
 
# 下载并解压 AriaNg 文件，这段代码会自动检测并下载最新版本 #
Ver=$(wget --no-check-certificate -qO- https://api.github.com/repos/mayswind/AriaNg/releases/latest | grep -o '"tag_name": ".*"' | sed 's/"//g;s/tag_name: //g') && echo ${Ver}
 
# 如果上面自动检测最新版本的代码返回空白或者错误，那么请访问 https://github.com/mayswind/AriaNg/releases/latest 来查看最新版本号。
# 例如手动获取的版本号是 0.5.0，那么手动执行命令： Ver="0.5.0" ，然后继续下面步骤即可。
 
wget -N --no-check-certificate "https://github.com/mayswind/AriaNg/releases/download/${Ver}/AriaNg-${Ver}.zip" && unzip AriaNg-${Ver}.zip && rm -rf AriaNg-${Ver}.zip
 
# 赋予虚拟主机文件夹权限 #
chmod -R 755 /usr/local/caddy/www/aria2
```
* 重启 Caddy
```
/etc/init.d/caddy restart
```
* 完成

## 使用Https/WebSocket(安全)协议连接Aria2
* 编辑Aria2配置文件aria2.conf
```
vi /root/.aria2c/aria2.conf
```
* 修改如下
```
#是否启用RPC服务的SSL/TLS加密
rpc-secure=true
#申请的域名crt证书文件路径，自行修改
rpc-certificate=/home/www.moerats.com.crt
##申请的域名key证书文件路径，自行修改
rpc-private-key=/home/www.moerats.com.key
```
* Aria2管理命令
```
启动：/etc/init.d/aria2 start

停止：/etc/init.d/aria2 stop

重启：/etc/init.d/aria2 restart

查看状态：/etc/init.d/aria2 status

配置文件：/root/.aria2/aria2.conf （配置文件包含中文注释，但是一些系统可能不支持显示中文）
```

