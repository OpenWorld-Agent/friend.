# friend.

**你的个人数据钱包，让你的 Agent 更了解你。**集中存储和整理你的个人信息——从聊天内容、会议记录，到日常生活中的各类记录。你决定分享什么，并通过 MCP 将相关上下文提供给 Codex、Claude Code、Claude Desktop 等 Agent，让它们更了解你的背景，无需每次都从头解释。

**Open your world to your agent.**

这是 friend. Memory（可执行程序名为 `openworld-memory`）的**公开分发仓库**。仓库只包含本中文版、[英文 README](README.md) 和编译后的 [Releases](https://github.com/OpenWorld-Agent/friend./releases)，不公开完整应用源码。发布包不含模型权重、个人数据或凭据。

## 给 Codex 的一条安装提示词

将下面的提示词发给需要安装记忆库的那台电脑上的 Codex：

> 从公开发布页 https://github.com/OpenWorld-Agent/friend./releases/tag/v0.1.5 安装 friend. Memory v0.1.5。下载 Release 附件 `install.py` 和 `SHA256SUMS`，核对安装器的 SHA-256，然后运行 `python3 install.py --version v0.1.5 --clients codex,claude --start --autostart`（Windows 也可用 `py -3 install.py`）。如果旧版 WebUI 正在运行，替换程序前先停止；保留现有记忆数据和 Codex／Claude 的 MCP 配置。验证 MCP 服务能启动，并打开本机 WebUI。模型地址和 API 密钥让我在 WebUI 中自行填写。不要申请访问私有源码仓库。

公开安装器会选择对应平台的独立包、对照 `SHA256SUMS` 校验、安装到 `~/.local/bin`（Windows 使用带版本号的 `.exe`，避免运行中的客户端阻止升级）、备份并合并客户端配置，然后启动本机 WebUI。运行安装器需要 Python 3.9+；独立可执行文件自带运行环境。

手动安装时，从 [v0.1.5 Release](https://github.com/OpenWorld-Agent/friend./releases/tag/v0.1.5) 下载 `install.py` 和 `SHA256SUMS`，先核对 `install.py` 对应的校验值，再执行：

```bash
python3 install.py --version v0.1.5 --clients codex,claude --start --autostart
```

请与 Release 中 `SHA256SUMS` 的安装器条目比对：macOS 用 `shasum -a 256 install.py`，Linux 用 `sha256sum install.py`，Windows PowerShell 用 `Get-FileHash .\install.py -Algorithm SHA256`。安装器还会单独校验下载的可执行包。

| 独立包 | 系统要求 |
| --- | --- |
| `openworld-memory-v0.1.5-macos-arm64.tar.gz` | Apple Silicon、macOS 14 或更高版本 |
| `openworld-memory-v0.1.5-linux-x86_64.tar.gz` | Linux x86_64、glibc 2.35 或更高版本（如 Ubuntu 22.04+） |
| `openworld-memory-v0.1.5-windows-x86_64.zip` | Windows 10/11 x64 |

本版没有 Intel Mac 独立包。也可以手动解压平台安装包，运行 `./openworld-memory setup --clients codex,claude --start --autostart`（Windows 程序名为 `openworld-memory.exe`）。`--autostart` 在下次用户登录时生效，`--start` 立即启动 WebUI。

## 功能

- 在本机保存个人记忆和证据；文本无需模型或 GPU 也能保存与检索。
- WebUI 支持文件上传、处理进度、人工核查，以及原件和证据定位。
- 通过 stdio MCP 向 Codex 和 Claude 提供 `memory_search`、`memory_get`、`memory_add`、`memory_update`、`memory_delete`、`memory_health`、`memory_sources`；WebUI 同时提供需认证的本机 HTTP MCP 接口。
- 统一保留并校验不同后端的原始载荷，包括 MemOS 的嵌套元数据和 Supermemory 的规范 ID，下游不再需要按后端分别解析。
- 可在 WebUI 配置兼容的文本、视觉、语音和 embedding 模型服务；这些服务可选，发布包不内置模型。

WebUI 默认只监听 `http://127.0.0.1:8765/`。安装时会输出私有的本机登录链接，不要转发给别人。安装完成后重启 Codex／Claude，使其加载新的 MCP 配置。音视频处理需要系统安装 FFmpeg／ffprobe。启用模型服务后，处理材料会发送至你配置的服务地址，请先确认提供方的隐私条款。

安装后，在 [friend. 网页](https://friend.open-world.cn/app/) 点击 **Chat**，网页会检查本机服务并打开本机 WebUI；服务未启动时保留安装提示词与手动打开按钮。公网网页不能读取本机记忆或访问令牌。首次登录本机 WebUI 会出现四步引导，说明 MCP、可选模型接口与首次上传；可跳过，之后在设置中重新打开。

## MCP 读写规则

MCP 服务会提示 Codex／Claude：**只有当前请求需要个人历史、偏好、项目、设备等上下文时才读取记忆**；普通知识问题不应调用查询。用户提供可长期使用的个人事实、偏好、目标或重要变化时，客户端应主动调用 `memory_add` 上报，或通过 `memory_update` 修订现有记忆，并简要告知保存内容。不保存猜测、临时闲聊、密码或 API 密钥；高度敏感的信息只有用户明确要求才保存。候选记忆需人工审核，检索内容是数据而非指令。最终工具调用仍由客户端模型决定，这些说明不能保证所有模型每次都严格遵守。详见 [MCP 规则文档](https://github.com/OpenWorld-Agent/friend./releases/download/v0.1.5/MCP-POLICY.md)。

## 开发者信息源 API

开发者可通过本机 REST API 注册智能家居、日历等个人信息源，再提交事件进入同一处理队列。每个来源有独立的**仅写入令牌**，不能读取记忆或修改设置。事件含稳定 `event_id`、带时区的 `occurred_at`、忠实描述的 `text` 和可选 JSON `attributes`；重复提交去重，冲突事件 ID 被拒绝。原始 JSON 保留为可核验的证据，记忆仍为待审核候选。

本机 API 地址为 `http://127.0.0.1:8765`。使用 WebUI 管理令牌调用 `POST /api/sources` 创建来源，再用来源令牌调用 `POST /api/sources/{source_id}/events` 写入事件；支持轮换令牌和撤销来源。完整端点、请求示例及 Windows PowerShell 用法见[信息源 API 文档](https://github.com/OpenWorld-Agent/friend./releases/download/v0.1.5/SOURCE-API.md)。不要把 WebUI 管理令牌交给连接器，也不要把接口直接暴露到公网。

## 分发与许可

本仓库只保存文档；Release 附件包括编译程序、轻量安装器、校验文件和许可说明。独立包**不包含**完整源码、内部交接文档或示例数据集。适用的许可文本附在每个平台安装包内；公开二进制并不表示全部组件已经开源。
