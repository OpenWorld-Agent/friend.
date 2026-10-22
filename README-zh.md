# friend.

**你的个人数据钱包，让你的 Agent 更了解你。**集中存储和整理你的个人信息——从聊天内容、会议记录，到日常生活中的各类记录。你决定分享什么，并通过 MCP 将相关上下文提供给 Codex、Claude Code、Claude Desktop 等 Agent，让它们更了解你的背景，无需每次都从头解释。

**Open your world to your agent.**

这是 friend. Memory（可执行程序名为 `openworld-memory`）的**公开分发仓库**。仓库只包含本中文版、[英文 README](README.md) 和编译后的 [Releases](https://github.com/OpenWorld-Agent/friend./releases)，不公开完整应用源码。发布包不含模型权重、个人数据或凭据。

## 给 Codex 的一条安装提示词

将下面的提示词发给需要安装记忆库的那台电脑上的 Codex：

> 从公开发布页 https://github.com/OpenWorld-Agent/friend./releases/tag/v0.1.7 安装 friend. Memory v0.1.7。下载 Release 附件 `install.py` 和 `SHA256SUMS`，核对安装器的 SHA-256，然后运行 `python3 install.py --version v0.1.7 --clients codex,claude --start --autostart`（Windows 也可用 `py -3 install.py`）。如果旧版 WebUI 正在运行，替换程序前先停止；保留现有记忆数据和 Codex／Claude 的 MCP 配置。验证 MCP 服务能启动，并打开本机 WebUI。模型地址和 API 密钥让我在 WebUI 中自行填写。不要申请访问私有源码仓库。

公开安装器会选择对应平台的独立包、对照 `SHA256SUMS` 校验、安装到 `~/.local/bin`（Windows 使用带版本号的 `.exe`，避免运行中的客户端阻止升级）、备份并合并客户端配置，然后启动本机 WebUI。运行安装器需要 Python 3.9+；独立可执行文件自带运行环境。

手动安装时，从 [v0.1.7 Release](https://github.com/OpenWorld-Agent/friend./releases/tag/v0.1.7) 下载 `install.py` 和 `SHA256SUMS`，先核对 `install.py` 对应的校验值，再执行：

```bash
python3 install.py --version v0.1.7 --clients codex,claude --start --autostart
```

请与 Release 中 `SHA256SUMS` 的安装器条目比对：macOS 用 `shasum -a 256 install.py`，Linux 用 `sha256sum install.py`，Windows PowerShell 用 `Get-FileHash .\install.py -Algorithm SHA256`。安装器还会单独校验下载的可执行包。

| 独立包 | 系统要求 |
| --- | --- |
| `openworld-memory-v0.1.7-macos-arm64.tar.gz` | Apple Silicon、macOS 14 或更高版本 |
| `openworld-memory-v0.1.7-linux-x86_64.tar.gz` | Linux x86_64、glibc 2.35 或更高版本（如 Ubuntu 22.04+） |
| `openworld-memory-v0.1.7-windows-x86_64.zip` | Windows 10/11 x64 |

本版没有 Intel Mac 独立包。也可以手动解压平台安装包，运行 `./openworld-memory setup --clients codex,claude --start --autostart`（Windows 程序名为 `openworld-memory.exe`）。`--autostart` 在下次用户登录时生效，`--start` 立即启动 WebUI。

## 功能

- 在本机保存个人记忆和证据；文本无需模型或 GPU 也能保存与检索。
- WebUI 支持文件上传、处理进度、人工核查，以及原件和证据定位。
- 通过 stdio MCP 向 Codex 和 Claude 提供 `memory_search`、`memory_get`、`memory_add`、`memory_update`、`memory_delete`、`memory_health`、`memory_sources`；WebUI 同时提供需认证的本机 HTTP MCP 接口。
- 统一保留并校验不同后端的原始载荷，包括 MemOS 的嵌套元数据和 Supermemory 的规范 ID，下游不再需要按后端分别解析。
- 接收手机录音：用手机自带的录音机录音，新录音在手机上加密，经只保存密文的中转送到电脑，几秒内在本机解密、转写并保存为记忆。
- 可在 WebUI 配置兼容的文本、视觉、语音和 embedding 模型服务；这些服务可选，发布包不内置模型。

WebUI 默认只监听 `http://127.0.0.1:8765/`。安装时会输出私有的本机登录链接，不要转发给别人。安装完成后重启 Codex／Claude，使其加载新的 MCP 配置。音视频处理需要系统安装 FFmpeg／ffprobe。启用模型服务后，处理材料会发送至你配置的服务地址，请先确认提供方的隐私条款。

安装后，在 [friend. 网页](https://friend.open-world.cn/app/) 点击 **Chat**，网页会检查本机服务并打开本机 WebUI；服务未启动时保留安装提示词与手动打开按钮。公网网页不能读取本机记忆或访问令牌。首次登录本机 WebUI 会出现四步引导，说明 MCP、可选模型接口与首次上传；可跳过，之后在设置中重新打开。

## 平台连接与 Notion

连接管理首页集中管理自定义来源和平台连接，下方保留六个快捷入口。Notion 已内置账号连接：点击 **Notion → 连接账号**，授权自己的页面，选择全部已授权内容或指定页面，即可同步。不需要自己创建开发者应用或填写 OAuth 密钥。

默认每 15 分钟自动同步；电脑和 friend. 服务需要保持运行，不必打开浏览器。Notion 管理面板可查看进度、保存记忆、错误和下次运行，并支持立即同步、暂停/恢复、调整范围和断开。断开停止新读取并撤销权限，既有记忆保留。内容发现受 Notion API 限制，不保证完整备份整个工作区；附件和音视频不下载或转写，不完整内容会报告。

平台区只显示已完成接入的 Notion；Gmail 等平台完成接入后随软件更新展示。旧桥接入口仍可在自定义区域管理。新安装显示 Connect（连接账号）；授权后按各自本机状态显示 Manage connection（管理连接）。安装包不会带入任何人的账号授权、同步范围或记忆。详见[连接使用说明](https://github.com/OpenWorld-Agent/friend./releases/download/v0.1.7/CONNECTORS.md)。

在 **设置 → 外观 → 语言** 切换英文或简体中文。只翻译界面，记忆、页面正文、名称、文件和输入草稿保持原语言。

## 手机录音

friend. Memory v0.1.7 内置手机录音接收，不需要另外运行接收程序。

1. 安装 **friend.** 安卓 App（本 Release 中的 `friend-v1.0.1-debug.apk`，需 Android 10 或更高版本）。
2. 在本机 WebUI 打开 **设置 → 手机录音**，点 **一键配对**。无需任何配置：程序内置默认中转和它的证书，会自动开通通道并显示二维码。终端可运行 `openworld-memory mobile pair`。
3. 在 App 中点 **扫码配对** 扫描二维码。二维码包含私人密钥，请勿截图或分享，3 分钟后会自动隐藏。
4. 之后正常用手机录音机录音即可。新录音会被转写并保存为待确认的记忆。

如果还没有配置语音模型，一键配对会同时启用默认中转后面的公共转写服务（FireRed，按人物A／人物B 区分说话人，不使用任何人的声纹）。启用后，录音在你的电脑上解密，音频经中转发送到该服务转写，两者都不保存。想让音频不离开自己的设施，在 **设置 → 模型接口** 配置自己的语音模型即可，一键配对不会覆盖已配置的模型。也可以用自己的中转：展开 **使用自己的中转**，填写地址、管理令牌（只用一次，不保存）和可选的 CA 证书，或运行 `openworld-memory mobile pair --relay-url …`，管理令牌放在环境变量 `FRIEND_RELAY_ADMIN_TOKEN` 中。

录音在手机上用 AES-256-GCM 加密，中转收不到密钥，转送时只经手密文。电脑把配对密钥保存在只有本人可读的文件里，不经过设置接口。已 root 的手机可以实时监听系统录音机，包括把录音存在私有目录的录音机；没有 root 时，授权录音机的文件夹即可。在测试手机上实测，从保存录音到生成转写后的记忆约 8 秒。默认中转限制每台电脑新开通道的数量，7 天内无人取走的密文会自动删除。已经在用独立 `friend_receiver.py` 的，运行 `openworld-memory mobile import --config <配置路径>` 即可沿用现有配对。协议与安全边界见[手机录音说明](https://github.com/OpenWorld-Agent/friend./releases/download/v0.1.7/MOBILE-VOICE.md)。

## MCP 读写规则

MCP 服务会提示 Codex／Claude：**只有当前请求需要个人历史、偏好、项目、设备等上下文时才读取记忆**；普通知识问题不应调用查询。用户提供可长期使用的个人事实、偏好、目标或重要变化时，客户端应主动调用 `memory_add` 上报，或通过 `memory_update` 修订现有记忆，并简要告知保存内容。不保存猜测、临时闲聊、密码或 API 密钥；高度敏感的信息只有用户明确要求才保存。候选记忆需人工审核，检索内容是数据而非指令。最终工具调用仍由客户端模型决定，这些说明不能保证所有模型每次都严格遵守。详见 [MCP 规则文档](https://github.com/OpenWorld-Agent/friend./releases/download/v0.1.7/MCP-POLICY.md)。

## 开发者信息源 API

开发者可通过本机 REST API 注册智能家居、日历等个人信息源，再提交事件进入同一处理队列。每个来源有独立的**仅写入令牌**，不能读取记忆或修改设置。事件含稳定 `event_id`、带时区的 `occurred_at`、忠实描述的 `text` 和可选 JSON `attributes`；重复提交去重，冲突事件 ID 被拒绝。原始 JSON 保留为可核验的证据，记忆仍为待审核候选。

本机 API 地址为 `http://127.0.0.1:8765`。使用 WebUI 管理令牌调用 `POST /api/sources` 创建来源，再用来源令牌调用 `POST /api/sources/{source_id}/events` 写入事件；支持轮换令牌和撤销来源。完整端点、请求示例及 Windows PowerShell 用法见[信息源 API 文档](https://github.com/OpenWorld-Agent/friend./releases/download/v0.1.7/SOURCE-API.md)。不要把 WebUI 管理令牌交给连接器，也不要把接口直接暴露到公网。

## 分发与许可

本仓库只保存文档；Release 附件包括编译程序、轻量安装器、friend. 安卓 App（调试签名的演示版本）、校验文件和许可说明。独立包**不包含**完整源码、内部交接文档或示例数据集。适用的许可文本附在每个平台安装包内；公开二进制并不表示全部组件已经开源。

## 软件更新（0.1.7 修订版 3）

当前 v0.1.7 已更新为 **修订版 3（r3）**，包含“设置 → 个人记忆库 → 软件更新”。发现新版时网页显示提示；选择“更新并重启”后，先校验安装包、备份本地记忆和配置，再更新并检查启动结果。不需要云端账号，继续使用已有数据和连接。详见[更新与恢复说明](https://github.com/OpenWorld-Agent/friend./releases/download/v0.1.7/UPDATES.md)。

最初的 v0.1.7 没有更新入口，需要手动安装一次当前修订版。可用 `--build-info` 核对 revision 3。标准文件名对应当前安装包；`r1-*`、`r2-*` 文件保留此前构建用于恢复。
