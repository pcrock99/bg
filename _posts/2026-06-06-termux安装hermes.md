---
layout: default
title: termux安装hermes
date: 2026-06-06
---

📱 Hermes Agent Termux 完整安装指南


📋 一、前置准备

硬件要求

· 安卓 8.0 或更高版本
· 推荐 6GB+ 内存（4GB 可运行基础功能）
· 至少 3GB 可用存储空间 

软件准备

· 安装 Termux（推荐从 F-Droid 下载，版本 ≥ v0.118.0）
· 准备好大模型 API Key（推荐 Kimi，国内直连，免费额度充足）

🔧 二、Termux 环境初始化

1. 安装 Termux 并授权

打开 Termux，执行：

```bash
termux-setup-storage
```

（会弹出权限请求，点允许）

2. 更新软件源

```bash
pkg update && pkg upgrade -y
```

3. 安装基础依赖

```bash
pkg install -y python clang git wget curl rust make cmake
```

clang 和 rust 是编译某些 Python 包必需的 

4. 验证 Python 环境

```bash
python --version   # 应显示 3.11 或更高
```

📦 三、安装 Hermes Agent

1. 一键安装命令（国内镜像，推荐） 

```bash
curl -fsSL https://res1.hermesagent.org.cn/install.sh | bash
```

这条命令会自动安装 Python 环境和所有依赖

2. 刷新环境变量

```bash
source ~/.bashrc
```

3. 验证安装

```bash
hermes --version
```

应显示类似 Hermes Agent v0.15.2 的版本号 

⚙️ 四、配置 API Key

1. 运行配置向导 

```bash
hermes setup
```

按提示选择模型提供方：

· 国内用户选 Moonshot / Kimi（列表里 Moonshot = Kimi）
· 输入你的 API Key

2. 或者直接编辑配置文件

```bash
nano ~/.hermes/config.yaml
```

修改 providers 部分，以 Kimi 为例：

```yaml
providers:
  kimi:
    provider: custom
    base_url: https://api.moonshot.cn/v1
    api_key: "你的API密钥"
```

保存后按 Ctrl+X，然后 Y，再回车。

🚀 五、启动与测试

1. 启动交互模式

```bash
hermes
```

看到 ╭─ ⚕ Hermes 提示符就成功了

2. 快速测试

```bash
hermes -m "用中文介绍你自己，一句话"
```

3. 常用命令

命令 作用
/model 查看/切换模型
/help 查看帮助
/exit 退出

🐛 六、常见问题与解决方案

问题1：pip3 未找到

```bash
pkg install python3-pip
```

问题2：ModuleNotFoundError: No module named 'dotenv'

```bash
pip install python-dotenv
```

问题3：hermes: command not found

方法一：重新加载环境

```bash
source ~/.bashrc
```

方法二：手动创建入口脚本 

```bash
# 找到 hermes_cli 位置
python -c "import hermes_cli; print(hermes_cli.__file__)"

# 创建启动脚本
cat > $PREFIX/bin/hermes << 'EOF'
#!/data/data/com.termux/files/usr/bin/python3
from hermes_cli.main import main
if __name__ == "__main__":
    main()
EOF

chmod +x $PREFIX/bin/hermes
```

问题4：安装脚本报 Directory exists but is not a git repository

```bash
rm -rf ~/.hermes/hermes-agent
```

然后重新运行安装命令

问题5：psutil 编译失败

```bash
pkg install python-psutil
```

💡 七、进阶配置

1. 添加 Ollama 云模型 

在 ~/.hermes/config.yaml 中添加：

```yaml
providers:
  ollama_cloud:
    provider: custom
    base_url: https://ollama.com/api/v1
    api_key: ${OLLAMA_API_KEY}

model_aliases:
  minimax-m3: ollama_cloud/minimax-m3:cloud
  qwen3.5: ollama_cloud/qwen3.5:cloud
```

2. 后台运行（防止 Termux 休眠中断）

```bash
pkg install tmux
tmux new -s hermes
hermes
# 按 Ctrl+B 然后按 D 退出会话（后台继续运行）
# 重新连接：tmux attach -t hermes
```

📌 八、安装验证清单

完成后逐一确认：

· hermes --version 输出版本号
· hermes 能进入交互界面
· 发送消息能收到回复
· 配置文件 ~/.hermes/config.yaml 存在且正确

📖 参考资源

· 官方文档：hermesagent.org.cn
· 国内镜像源安装命令：curl -fsSL https://res1.hermesagent.org.cn/install.sh | bash 

---