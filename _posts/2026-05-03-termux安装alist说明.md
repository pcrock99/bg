---
layout: post
title: Termux 安装部署 Alist 最新版详细教程｜手机免 Root 搭建全网网盘聚合神器
date: 2026-05-03 00:00:00 +0800
categories: [Termux, Alist]
tags: [Termux, Alist, 网盘聚合, 手机建站, 云盘挂载, 自建网盘]
toc: true
comments: true
---
Termux 安装部署 Alist 最新版详细教程｜手机免 Root 搭建全网网盘聚合神器

前言
Alist 是目前最强的多网盘聚合管理工具，支持阿里云盘、百度网盘、夸克、123、OneDrive、谷歌云盘等几乎所有网盘，统一后台管理、在线播放、直链下载、网页浏览。
本篇教程手把手教你在安卓手机 Termux 免 Root 环境下，从零安装、部署、设置管理员密码、后台常驻、开机自启、手机/电脑随时访问 Alist 网盘后台。
低配手机也能跑，无需服务器，手机 24 小时挂着就是你的私人网盘服务器。

---
一、前期准备工作
1. Termux 基础环境必须就绪
确保 Termux 已经更新基础环境，如果没执行过，先运行：
pkg update && pkg upgrade -y
pkg install wget curl tar zip unzip -y
2. 开启手机存储权限（可选）
如果需要 Alist 读取手机本地文件做本地网盘，执行授权：
termux-setup-storage

---
二、Termux 一键下载安装最新版 Alist
1. 进入家目录，新建 Alist 文件夹
cd ~
mkdir -p alist && cd alist
2. 下载 Termux 适配版 Alist 二进制文件
Termux 是 arm 架构，直接下载官方 Linux arm64 版本即可，一键下载：
wget https://github.com/alist-org/alist/releases/latest/download/alist-linux-arm64-musl.tar.gz
3. 解压压缩包
tar -zxvf alist-linux-arm64-musl.tar.gz
4. 赋予运行权限
chmod +x alist

---
三、初始化 Alist 并设置管理员密码
1. 首次初始化，生成管理员账号密码
./alist admin random
执行后会自动生成随机管理员密码，记得复制保存。
2. 自定义设置自己的管理员密码（推荐）
./alist admin set 你的自定义密码
示例：
./alist admin set 12345678

---
四、前台运行 Alist 测试是否正常
1. 启动 Alist
./alist server
2. 访问 Alist 后台
启动成功会提示监听端口，默认端口：5244
浏览器打开地址：
http://localhost:5244
同一局域网电脑访问把 localhost 换成你手机内网 IP 即可。
能打开网页就说明安装成功，按 Ctrl + C 先暂停前台运行，下一步配置后台常驻。

---
五、配置 Alist 后台永久挂后台运行（保活不退出）
直接前台运行关闭终端就闪退，我们用 nohup 常驻后台运行。
1. 后台启动 Alist 命令
nohup ./alist server > alist.log 2>&1 
2. 查看 Alist 运行日志
cat alist.log
3. 关闭/停止 Alist 运行
pkill alist

---
六、设置 Termux 开机自启 Alist（重启手机自动运行）
1. 编辑 Termux 开机启动配置文件
vim ~/.bashrc
2. 在文件最后一行添加自启命令
cd ~/alist && nohup ./alist server > alist.log 2>&1 
保存退出：按 ESC 输入 :wq 回车。
以后每次打开 Termux，Alist 自动后台启动，不用手动运行。

---
七、局域网设备访问 Alist 后台
1. 查看手机内网 IP
ifconfig
2. 电脑/电视/平板访问地址格式
http://手机内网IP:5244
输入管理员账号密码即可进入后台添加网盘。

---
八、简单外网访问思路（可选进阶）
想要外面手机流量也能访问自己的 Alist，可以使用：
- 内网穿透工具
- FRP 穿透
- Cloudflare Tunnel 免费穿透
需要我可以再单独写一篇 Termux Alist 外网访问教程。

---
九、常见问题报错解决
1. 启动提示端口被占用
pkill alist
杀掉进程重新后台启动即可。
2. Termux 重启 Alist 没自启
检查 .bashrc 文件是否添加成功，重新添加一次即可。
3. 网页打不开 Alist
检查手机和电脑同一 WiFi，关闭手机省电优化，Termux 后台锁定不被杀。

---
结语
现在你的安卓手机已经成功变身私人网盘聚合服务器，搭配 Alist 所有网盘统一管理、在线播放视频、直链下载，不用花钱买会员，手机 24 小时挂机就能用。
后续添加网盘挂载、美化 Alist 界面、设置账号访客、开启直链加速，我都可以继续给你写配套教程。