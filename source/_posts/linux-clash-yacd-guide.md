---
title: linux_clash_yacd_guide
date: 2023-04-14 13:50:37
tags:
---

# Linux系统配置clash教程

## clash下载
在[clash release](https://github.com/Dreamacro/clash/releases)页面下载linux对应版本，使用SFTP传到Linux服务器
然后解压压缩包，并为clash添加可执行权限：
```shell
gunzip clash-linux-amd64-v1.14.0.gz
mv clash-linux-amd64-v1.14.0 clash
chmod u+x clash
```

访问[Country.mmdb](https://github.com/Dreamacro/maxmind-geoip/releases)下载clash运行所需文件,然后使用SFTP传到Linux服务器文件夹——`~/.config/clash`

## 配置文件
将linux服务器文件夹`~/.config/clash`下的`config.yaml`文件配置为自己订阅的服务

在该`config.yaml`中添加：
```yaml
secret: xxxxx #api访问密钥，安全所需，可不加
external-controller: 0.0.0.0:9090 #外部访问接口
external-ui: dashboard #外部访问客户端
```
## 下载yacd
```shell
cd ~/.config/clash
wget https://github.com/haishanh/yacd/archive/gh-pages.zip
unzip gh-pages.zip
mv yacd-gh-pages/ dashboard/
```

访问[yacd](39.105.14.234:9090/ui)，配置对应clash

## 配置clash开机自启动
将以下脚本保存为`/etc/systemd/system/clash.service`：
```shell
[Unit]
Description=Clash service
After=network.target

[Service]
Type=simple
User=randyliu
ExecStart=/home/randyliu/Downloads/clash/clash #Clash 程序路径
Restart=on-failure
RestartPreventExitStatus=23

[Install]
WantedBy=multi-user.target
```

用systemctl管理服务
```shell
# 重载服务
sudo systemctl daemon-reload
# 开机启动
sudo systemctl enable clash
# 启动服务
sudo systemctl start clash
# 查看服务状态
sudo systemctl status clash
```

## 设置代理
在`~/.bashrc`中添加：
```shell
export http_proxy=127.0.0.1:7890
export https_proxy=127.0.0.1:7890
```

*如果使用oh my zsh, `~/.bashrc`文件变为`~/.zshrc`*

保存后运行`source ~/.bashrc`使配置生效

重启服务器，运行`sudo systemctl status clash`检查clash服务状态

测试连通性：`wget "www.youtube.com"`

