# Ubuntu 虚拟机安装 OpenClaw 完整流程

本教程适用于在 Ubuntu 虚拟机环境下，通过官方脚本和 Opencode 工具部署 OpenClaw。

## 目录

- [1. 基础环境配置（免密设置）](#1-基础环境配置免密设置)
- [2. 安装基础工具与 OpenSSH](#2-安装基础工具与-openssh)
- [3. 安装 Opencode 工具](#3-安装-opencode-工具)
- [4. 安装 OpenClaw (官方脚本)](#4-安装-openclaw-官方脚本)
- [5. 调用教程完成后续配置](#5-调用教程完成后续配置)
- [6. 浏览器扩展安装](#6-浏览器扩展安装)

---

## 1. 基础环境配置（免密设置）

为了在后续安装脚本运行中避免频繁输入密码，我们需要为当前用户开启 `sudo` 免密权限。

### 1.1 编辑 sudoers 配置

```bash
sudo visudo
```

### 1.2 添加免密权限

在文件末尾添加以下内容（将 `spoto` 替换为你实际的用户名）：

```
spoto ALL=(ALL) NOPASSWD: ALL
```

### 1.3 保存并退出

- `nano` 编辑器：按 `Ctrl+O` 保存，`Enter` 确认，`Ctrl+X` 退出
- `vim` 编辑器：输入 `:wq` 保存退出

---

## 2. 安装基础工具与 OpenSSH

确保系统更新，并安装远程连接及脚本下载所需的组件。

```bash
# 更新系统包列表
sudo apt update

# 安装基础下载工具
sudo apt install -y curl

# 安装并启动 OpenSSH 服务（方便通过 PC 远程管理）
sudo apt install -y openssh-server
```

### 2.1 验证 SSH 服务状态

```bash
# 检查 SSH 服务状态
sudo systemctl status ssh

# 如果服务未启动，使用以下命令启动
sudo systemctl start ssh
```

### 2.2 获取虚拟机 IP 地址

```bash
# 获取局域网 IP 地址，用于远程连接
ip addr show | grep -E "inet " | awk '{print $2}' | cut -d'/' -f1 | grep -v "^127"
```

记录此 IP 地址，后续可通过 SSH 远程管理虚拟机。

---

## 3. 安装 Opencode 工具

Opencode 用于简化后续的复杂配置过程。

```bash
curl -fsSL https://opencode.ai/install | bash
```

### 3.1 验证安装

```bash
# 检查 Opencode 是否安装成功
which opencode

# 查看版本
opencode --version
```

---

## 4. 安装 OpenClaw (官方脚本)

使用官方提供的一键安装脚本进行部署：

```bash
curl -fsSL https://molt.bot/install.sh | bash
```

### 4.1 运行初始化向导

安装完成后，运行初始化向导完成基本配置：

```bash
openclaw onboard --install-daemon
```

按照向导提示完成配置：
- 选择安全选项（理解风险）
- 配置工作区目录
- 设置 Gateway 认证方式
- 其他基本配置

### 4.2 验证安装

```bash
# 检查 openclaw 是否安装成功
which openclaw

# 查看版本
openclaw --version

# 检查 Gateway 状态
openclaw gateway status
```

---

## 5. 调用教程完成后续配置

利用 Opencode 加载司波图团队整理的专用教程，引导完成后续的模型配置和局域网访问设置。

### 5.1 教程地址

```
https://github.com/spoto-team/openclaw-minimax-guide
```

### 5.2 执行命令

在终端输入以下命令：

```bash
opencode run https://github.com/spoto-team/openclaw-minimax-guide
```

### 5.3 后续配置内容

该教程将引导你完成以下配置：

#### 5.3.1 国内 API 配置

- 配置 MiniMax 国内版 API（推荐）
- 配置智谱 GLM-4.7 API
- 获取 API Key
- 编辑配置文件

**相关文档**：[OpenClaw 国内 API 替换配置指南](./OPENCLAW-CN-API-GUIDE.md)

#### 5.3.2 局域网访问配置

- 配置 Gateway 支持局域网访问
- 获取 Web UI 访问地址
- 获取登录 Token
- 配置 HTTP 安全访问

**相关文档**：[OpenClaw 局域网访问配置指南](./OPENCLAW-LAN-ACCESS-GUIDE.md)

### 5.4 完成配置

请根据屏幕提示完成以下步骤：

1. ✅ 获取并配置 API Key
2. ✅ 编辑配置文件 `~/.openclaw/openclaw.json`
3. ✅ 重启 Gateway
4. ✅ 验证模型配置
5. ✅ 配置局域网访问（如需要）
6. ✅ 登录 Web UI 验证

---

## 6. 浏览器扩展安装

如需在 OpenClaw 中调用浏览器功能，请在终端执行扩展安装命令：

```bash
openclaw browser extension install
```

### 6.1 验证浏览器扩展

```bash
# 检查浏览器扩展状态
openclaw browser status
```

---

## 常见问题

### Q1: sudo 免密设置失败

**问题**：执行 `sudo visudo` 时权限不足

**解决方案**：

```bash
# 切换到 root 用户
su -

# 然后执行 visudo
visudo
```

### Q2: SSH 无法连接

**问题**：从主机无法 SSH 连接虚拟机

**解决方案**：

1. 检查虚拟机防火墙：
   ```bash
   sudo ufw status
   sudo ufw allow ssh
   ```

2. 检查 SSH 服务：
   ```bash
   sudo systemctl restart ssh
   ```

3. 确认虚拟机 IP 地址正确

### Q3: OpenClaw 安装失败

**问题**：官方脚本安装失败

**解决方案**：

1. 检查系统要求：
   ```bash
   node --version  # 需要 Node.js ≥ 22
   ```

2. 清理后重试：
   ```bash
   npm uninstall -g openclaw
   npm cache clean --force
   curl -fsSL https://molt.bot/install.sh | bash
   ```

3. 查看错误日志：
   ```bash
   tail -f ~/.openclaw/logs/gateway.log
   ```

### Q4: Opencode 教程加载失败

**问题**：无法加载外部教程

**解决方案**：

1. 检查网络连接
2. 手动克隆教程仓库：
   ```bash
   git clone https://github.com/spoto-team/openclaw-minimax-guide.git
   cd openclaw-minimax-guide
   ```
3. 按照本地文档完成配置

---

## 后续步骤

完成本教程后，你将拥有：

- ✅ 运行在 Ubuntu 虚拟机上的 OpenClaw
- ✅ 配置完成的国内 API（MiniMax 或智谱）
- ✅ 可通过局域网访问的 Web UI
- ✅ 可选的浏览器扩展功能

## 相关资源

- [OpenClaw 官方文档](https://docs.clawd.bot)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [MiniMax 官网](https://www.minimaxi.com)
- [智谱 AI 官网](https://bigmodel.cn)
- [Opencode 官网](https://opencode.ai)

---

## 许可证

MIT License - 详见 [LICENSE](./LICENSE) 文件

---

## 版本信息

- 文档版本: 1.0
- 创建日期: 2026-01-29
- 适用系统: Ubuntu 20.04/22.04
- 支持的 OpenClaw 版本: 2026.1.24-3+

**注意**：请妥善保管您的 API Key、Token 和虚拟机访问凭证，定期检查系统安全。
