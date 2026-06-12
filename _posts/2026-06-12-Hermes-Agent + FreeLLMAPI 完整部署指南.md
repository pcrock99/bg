---
layout: docs
title: Hermes-Agent 对接 FreeLLMAPI 完整部署文档
date: 2026-06-12
---
# Hermes-Agent + FreeLLMAPI 完整部署指南
## 前置环境依赖
### 系统必备软件
- Git
- Python 3.10 ~ 3.11
- Miniconda / Anaconda（推荐环境隔离）
### 硬件最低标准
- CPU ≥4核，内存 ≥8GB
- 磁盘预留 10GB 以上（源码+模型文件）
---
## 一、FreeLLMAPI 安装与配置
### 1.1 拉取源码

```bash
git clone https://github.com/FreeLLM/FreeLLMAPI.git
cd FreeLLMAPI
### 1.2 独立虚拟环境搭建
# 创建隔离环境
conda create -n freellmapi python=3.11 -y
# 激活环境
conda activate freellmapi
# 安装依赖（清华源加速）
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
### 1.3 FreeLLMAPI 核心配置 config.yaml

项目根目录新建 / 修改 config.yaml：

```bash
server:
  host: 0.0.0.0
  port: 8000
  workers: 1
  api_key: sk-freellm-000001
```

```bash
model:
  type: llama_cpp
  model_path: ./models/BitNet-b1.58-2B-4T/ggml-model-i2_s.gguf
  n_ctx: 2048
  n_threads: 6
  n_gpu_layers: 0
  temperature: 0.7
  max_tokens: 1024
  stop: ["用户：", "\n\n"]
```

```bash
cors:
  allow_origins:
    - http://127.0.0.1
    - http://localhost
    - http://127.0.0.1:7860
    - http://127.0.0.1:8080
```

### 1.4 启动 FreeLLMAPI 服务
调试前台运行

```
python main.py

```
Linux/macOS 后台常驻

```
nohup python main.py > api.log 2>&1 &
```
Windows PowerShell 静默后台
```
Start-Process python "main.py" -WindowStyle Hidden
```
### 1.5 API 连通性测试命令
```
curl http://127.0.0.1:8000/v1/chat/completions \
-H "Authorization: Bearer sk-freellm-000001" \
-H "Content-Type: application/json" \
-d '{
    "model": "bitnet-2b",
    "messages": [{"role":"user","content":"你好"}]
}'
```
## 二、Hermes-Agent 对接 FreeLLMAPI
### 2.1 Hermes 环境初始化
```
# 克隆 Hermes-Agent 源码
git clone https://github.com/xxx/Hermes-Agent.git
cd Hermes-Agent
# 创建独立环境
conda create -n hermes python=3.11 -y
conda activate hermes
# 安装依赖
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```
### 2.2 Hermes 服务商配置 providers.yaml
替换原有 providers.yaml 全部内容：
```
providers:
  freellm:
    enable: true
    base_url: http://127.0.0.1:8000/v1
    api_key: sk-freellm-000001
    models:
      - bitnet-2b
    timeout: 120
    temperature: 0.7
    max_context: 2048

openai:
  enable: false
zhipu:
  enable: false
volcengine:
  enable: false
nvidia:
  enable: false
```
### 2.3 Hermes 主配置 config.yaml 关键片段
```
llm:
  default_provider: freellm
  default_model: bitnet-2b
  system_prompt: "你是基于BitNet 1.58bit轻量化本地AI助手，回答简洁准确。"

server:
  host: 127.0.0.1
  port: 7860
  webui: true
```
### 2.4 启动 Hermes-Agent
命令行交互模式
```
conda activate hermes
python main.py
```
WebUI 可视化面板（推荐）
```
conda activate hermes
python webui.py
```
访问地址：http://127.0.0.1:7860
## 三、一键启停脚本
Linux/macOS 启动脚本 start_all.sh
```
#!/bin/bash
# 启动 FreeLLMAPI
cd ./FreeLLMAPI
conda activate freellmapi
nohup python main.py > api.log 2>&1 &
echo "FreeLLMAPI 服务已启动，端口:8000"
sleep 3

# 启动 Hermes WebUI
cd ../Hermes-Agent
conda activate hermes
python webui.py
```
Windows PowerShell 启动脚本 start_all.ps1
```
# 启动 FreeLLMAPI 后端
Set-Location .\FreeLLMAPI
conda activate freellmapi
Start-Process python "main.py" -WindowStyle Hidden
Write-Host "FreeLLMAPI 后台启动完成，端口8000"
Start-Sleep 3

# 启动 Hermes WebUI
Set-Location ..\Hermes-Agent
conda activate hermes
python webui.py
```
## 四、端口对照表
服务名称|访问地址|功能说明
---|---|---
FreeLLMAPI|http://127.0.0.1:8000/v1|LLM 推理后端接口
Hermes WebUI|http://127.0.0.1:7860|Agent 可视化操作面板

## 五、常见故障排查
### 5.1 Hermes 401 鉴权失败
providers.yaml 与 FreeLLMAPI config.yaml 内 api_key 必须完全一致。
### 5.2 BitNet i2_s 模型加载失败
FreeLLMAPI 内置 llama.cpp 无 BitNet 专用算子，两种解决方案：
改用微软 BitNet 自带 llama-server 替代 FreeLLMAPI
替换 FreeLLMAPI 底层为支持 BitNet 的魔改 llama.cpp
### 5.3 API 连接超时
FreeLLMAPI 配置 host: 0.0.0.0 允许外部局域网访问
防火墙放行 8000、7860 端口
确认 FreeLLMAPI 进程正常运行
### 5.4 运行内存溢出
- 下调 n_ctx: 1024
- 关闭后台占用内存程序
- 优先使用 2B 轻量化 BitNet 模型