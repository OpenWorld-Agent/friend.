# friend.

**供 Codex 和 Claude 使用的个人记忆数据库。**在本机 WebUI 上传文件，将材料处理为有来源和证据的记忆，人工核查后通过 MCP 查询同一份数据库。

这是 friend. Memory（可执行程序名为 `openworld-memory`）的**公开分发仓库**。仓库只包含本中文版、[英文 README](README.md) 和编译后的 [Releases](https://github.com/OpenWorld-Agent/friend./releases)，不公开完整应用源码。发布包不含模型权重、个人数据或凭据。

## 给 Codex 的一条安装提示词

将下面的提示词发给需要安装记忆库的那台电脑上的 Codex：

> 从公开发布页 https://github.com/OpenWorld-Agent/friend./releases/tag/v0.1.2 安装 friend. Memory v0.1.2。下载 Release 附件 `install.py` 和 `SHA256SUMS`，核对安装器的 SHA-256，然后运行 `python3 install.py --version v0.1.2 --clients codex,claude --start --autostart`。如果旧版 WebUI 正在运行，替换程序前先停止；保留现有记忆数据和 Codex／Claude 的 MCP 配置。验证 MCP 服务能启动，并打开本机 WebUI。模型地址和 API 密钥让我在 WebUI 中自行填写。不要申请访问私有源码仓库。

公开安装器会选择对应平台的独立包、对照 `SHA256SUMS` 校验、安装到 `~/.local/bin/openworld-memory`、备份并合并客户端配置，然后启动本机 WebUI。运行安装器需要 Python 3.9+；独立可执行文件自带运行环境。

手动安装时，从 [v0.1.2 Release](https://github.com/OpenWorld-Agent/friend./releases/tag/v0.1.2) 下载 `install.py` 和 `SHA256SUMS`，先核对 `install.py` 对应的校验值，再执行：

```bash
python3 install.py --version v0.1.2 --clients codex,claude --start --autostart
```

本版安装器的 SHA-256 为 `960fc5d4afe7ad0aec421a73188aee4231b55d528f51d2cabde4fb6a3fce3146`。macOS 可用 `shasum -a 256 install.py`，Linux 可用 `sha256sum install.py`；安装器还会单独校验下载的可执行包。

| 独立包 | 系统要求 |
| --- | --- |
| `openworld-memory-v0.1.2-macos-arm64.tar.gz` | Apple Silicon、macOS 14 或更高版本 |
| `openworld-memory-v0.1.2-linux-x86_64.tar.gz` | Linux x86_64、glibc 2.35 或更高版本（如 Ubuntu 22.04+） |

本版没有 Windows 或 Intel Mac 独立包。也可以手动解压平台安装包，运行 `./openworld-memory setup --clients codex,claude --start --autostart`。`--autostart` 在下次用户登录时生效，`--start` 立即启动 WebUI。

## 功能

- 在本机保存个人记忆和证据；文本无需模型或 GPU 也能保存与检索。
- WebUI 支持文件上传、处理进度、人工核查，以及原件和证据定位。
- 通过 stdio MCP 向 Codex 和 Claude 提供 `memory_search`、`memory_get`、`memory_add`、`memory_delete`、`memory_health`、`memory_sources`；WebUI 同时提供需认证的本机 HTTP MCP 接口。
- 可在 WebUI 配置兼容的文本、视觉、语音和 embedding 模型服务；这些服务可选，发布包不内置模型。

WebUI 默认只监听 `http://127.0.0.1:8765/`。安装时会输出私有的本机登录链接，不要转发给别人。安装完成后重启 Codex／Claude，使其加载新的 MCP 配置。音视频处理需要系统安装 FFmpeg／ffprobe。启用模型服务后，处理材料会发送至你配置的服务地址，请先确认提供方的隐私条款。

## 分发与许可

本仓库只保存文档；Release 附件包括编译程序、轻量安装器、校验文件和许可说明。独立包**不包含**完整源码、内部交接文档或示例数据集。适用的许可文本附在每个平台安装包内；公开二进制并不表示全部组件已经开源。
