# friend.

**A personal memory database for Codex and Claude.** Upload files in the local WebUI, process them into source-backed memories, review the results, and query the same database through MCP.

This is the **public distribution repository** for friend. Memory (the `openworld-memory` application). It contains this English README, a [Chinese README](README-zh.md), and compiled [Releases](https://github.com/OpenWorld-Agent/friend./releases). The complete application source code is not published here. No model weights, personal data, or credentials are included in the releases.

## Install with one Codex prompt

Copy this prompt into Codex on the computer where you want the memory database:

> Install friend. Memory v0.1.3 from the public release at https://github.com/OpenWorld-Agent/friend./releases/tag/v0.1.3. Download the `install.py` and `SHA256SUMS` release assets, verify the installer's SHA-256, then run `python3 install.py --version v0.1.3 --clients codex,claude --start --autostart`. If an older WebUI is running, stop it before replacing the executable; preserve my existing memory data and Codex/Claude MCP configurations. Verify that the MCP server starts and open the local WebUI. Let me enter any model endpoints or API keys in the WebUI. Do not request access to the private source repository.

The public installer selects the correct native archive, checks it against `SHA256SUMS`, installs `openworld-memory` under `~/.local/bin`, backs up and merges supported client settings, and starts the local WebUI. Python 3.9+ is needed to run the installer; the native executable includes its own runtime.

For a manual installation, download `install.py` and `SHA256SUMS` from the [v0.1.3 release](https://github.com/OpenWorld-Agent/friend./releases/tag/v0.1.3), verify `install.py` against its line in `SHA256SUMS`, then run:

```bash
python3 install.py --version v0.1.3 --clients codex,claude --start --autostart
```

The release's installer SHA-256 is `39b6050963908d3026f0847119e1ced3309d025eb999686e1e99b57e7f245d5d`. On macOS, `shasum -a 256 install.py` prints the digest; on Linux, use `sha256sum install.py`. The installer verifies the downloaded executable archive separately.

| Native package | Requirements |
| --- | --- |
| `openworld-memory-v0.1.3-macos-arm64.tar.gz` | macOS 14 or later on Apple Silicon |
| `openworld-memory-v0.1.3-linux-x86_64.tar.gz` | Linux x86_64 with glibc 2.35 or later (for example, Ubuntu 22.04+) |

There is no native Windows or Intel Mac package in this release. Extracting a native archive directly and running `./openworld-memory setup --clients codex,claude --start --autostart` is also supported. The optional `--autostart` setting activates at the next user login; `--start` starts the WebUI now.

## What it does

- Stores personal memories and evidence locally. Text can be saved and searched without a model or GPU.
- Accepts file uploads in the WebUI, tracks processing, and retains original files and evidence links for review.
- Exposes `memory_search`, `memory_get`, `memory_add`, `memory_delete`, `memory_health`, and `memory_sources` through stdio MCP for Codex and Claude. The WebUI also serves an authenticated local HTTP MCP endpoint.
- Lets you configure compatible text, vision, speech, and embedding model endpoints in the WebUI. Those services are optional and are not bundled.

The WebUI listens on `http://127.0.0.1:8765/` by default. Setup prints a private local login link; do not share it. Restart Codex and Claude after installation so they load the new MCP configuration. Audio and video processing requires system FFmpeg/ffprobe. Content sent to a model provider follows the endpoint you configure; review that provider's privacy policy before enabling it.

After installation, the **Chat** tab at [friend.open-world.cn/app/](https://friend.open-world.cn/app/) checks for the local app and opens its WebUI on your computer. If it is unavailable, Chat shows the installation prompt and a manual Open local WebUI button. The website does not read your memory or local access token. On first login, the local WebUI presents a four-step guide for MCP, optional model endpoints, and the first upload; you can skip it and reopen it later in settings.

## Distribution and licenses

This repository intentionally contains documentation only; release assets contain compiled executables, a small installer, checksums, and bundled license notices. The release archives do **not** contain the full source tree, development handoff documents, or example datasets. Applicable license texts are included inside each native archive. Publishing these binaries does not make every component open source.
