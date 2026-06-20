---
layout: default
title: "Hermes Agent Termux (Android) 升级经验"
date: 2026-06-20
categories: [android, termux, hermes, python]
tags: [hermes-agent, termux, android, pip, rust, pydantic-core, upgrade]
---

Hermes Agent 在 Termux (Android 12, aarch64) 上升级时，`pip install` 会因为 Rust 扩展包 (pydantic-core, watchfiles) 编译失败而报错。以下是完整的解决方案和加速技巧。

## 一、问题

在 Termux (Android 12, aarch64) 上 `pip install hermes-agent` 升级时，构建 Rust 扩展包 (pydantic-core, watchfiles) 失败，错误信息：

```
Failed to determine Android API level. Please set the ANDROID_API_LEVEL
environment variable.
```

## 二、原因

maturin (Rust/Python 构建工具) 需要知道 Android API level 来编译 cdylib 动态库。Termux 环境默认没有设置此变量。

## 三、解决方法

```bash
export ANDROID_API_LEVEL=31
```

(Android 12 对应 API level 31)

或加入 `~/.bashrc` 使其持久化：

```bash
echo 'export ANDROID_API_LEVEL=31' >> ~/.bashrc
```

## 四、完整升级命令

```bash
export ANDROID_API_LEVEL=31
pip install hermes-agent==0.17.0
```

## 五、编译的包（耗时最长）

| 包名 | 编译时间 |
|------|---------|
| pydantic-core (Rust) | ~5 分钟 |
| watchfiles (Rust) | ~3 分钟 |
| uvloop | ~2 分钟 |
| MarkupSafe | ~30 秒 |
| httptools | ~20 秒 |
| ruamel.yaml.clib | ~20 秒 |

## 六、加速技巧

使用另一台已装好正常环境的手机预编译 `.whl` 文件，然后传输到目标设备离线安装。

在有网络的环境上预编译：

```bash
pip wheel hermes-agent==0.17.0 -w ~/hermes-wheels/
```

传输 `.whl` 文件到目标设备后，离线安装：

```bash
pip install --no-index --find-links ~/hermes-wheels/ hermes-agent==0.17.0
```

## 七、其他修复

升级前需清理 `/usr/bin/hermes` 损坏的符号链接：

```bash
rm /data/data/com.termux/files/usr/bin/hermes
```

## 八、版本信息

| 项目 | 版本 |
|------|------|
| 旧版本 | hermes-agent v0.16.0 |
| 新版本 | hermes-agent v0.17.0 |
| OpenAI SDK | 2.43.0 → 2.24.0（hermes 0.17.0 固定版本） |
