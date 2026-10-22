# friend.

**friend. is your personal data wallet for AI.** Store and organize your personal information—from conversations and meeting notes to records of your everyday life—and choose what to share with the agents you use. Through MCP, friend. connects your data to your agents, giving them the context to understand you and provide more personalized help, without making you explain everything from scratch.

**Open your world to your agent.**

This is the **public distribution repository** for friend. Memory (the `openworld-memory` application). It contains this English README, a [Chinese README](README-zh.md), and compiled [Releases](https://github.com/OpenWorld-Agent/friend./releases). The complete application source code is not published here. No model weights, personal data, or credentials are included in the releases.

## Install with one Codex prompt

Copy this prompt into Codex on the computer where you want the memory database:

> Install friend. Memory v0.1.7 from the public release at https://github.com/OpenWorld-Agent/friend./releases/tag/v0.1.7. Download the `install.py` and `SHA256SUMS` release assets, verify the installer's SHA-256, then run `python3 install.py --version v0.1.7 --clients codex,claude --start --autostart` (on Windows, `py -3 install.py` also works). If an older WebUI is running, stop it before replacing the executable; preserve my existing memory data and Codex/Claude MCP configurations. Verify that the MCP server starts and open the local WebUI. Let me enter any model endpoints or API keys in the WebUI. Do not request access to the private source repository.

The public installer selects the correct native archive, checks it against `SHA256SUMS`, installs under `~/.local/bin` (using a versioned `.exe` on Windows so running clients do not block upgrades), backs up and merges supported client settings, and starts the local WebUI. Python 3.9+ is needed to run the installer; the native executable includes its own runtime.

For a manual installation, download `install.py` and `SHA256SUMS` from the [v0.1.7 release](https://github.com/OpenWorld-Agent/friend./releases/tag/v0.1.7), verify `install.py` against its line in `SHA256SUMS`, then run:

```bash
python3 install.py --version v0.1.7 --clients codex,claude --start --autostart
```

Compare the installer with its line in the release's `SHA256SUMS`. On macOS, use `shasum -a 256 install.py`; on Linux, use `sha256sum install.py`; on Windows PowerShell, use `Get-FileHash .\install.py -Algorithm SHA256`. The installer verifies the downloaded executable archive separately.

| Native package | Requirements |
| --- | --- |
| `openworld-memory-v0.1.7-macos-arm64.tar.gz` | macOS 14 or later on Apple Silicon |
| `openworld-memory-v0.1.7-linux-x86_64.tar.gz` | Linux x86_64 with glibc 2.35 or later (for example, Ubuntu 22.04+) |
| `openworld-memory-v0.1.7-windows-x86_64.zip` | Windows 10/11 x64 |

There is no native Intel Mac package in this release. Extracting a native archive directly and running `./openworld-memory setup --clients codex,claude --start --autostart` (or `openworld-memory.exe` on Windows) is also supported. The optional `--autostart` setting activates at the next user login; `--start` starts the WebUI now.

## What it does

- Stores personal memories and evidence locally. Text can be saved and searched without a model or GPU.
- Accepts file uploads in the WebUI, tracks processing, and retains original files and evidence links for review.
- Exposes `memory_search`, `memory_get`, `memory_add`, `memory_update`, `memory_delete`, `memory_health`, and `memory_sources` through stdio MCP for Codex and Claude. The WebUI also serves an authenticated local HTTP MCP endpoint.
- Preserves and validates canonical raw payloads across backends, including MemOS nested metadata and Supermemory canonical identifiers, so clients do not need backend-specific parsing.
- Receives recordings from your phone: record with the phone's normal recorder, and each new recording is encrypted on the phone, passed through a relay that stores only ciphertext, then decrypted, transcribed and saved on your computer within seconds.
- Lets you configure compatible text, vision, speech, and embedding model endpoints in the WebUI. Those services are optional and are not bundled.

The WebUI listens on `http://127.0.0.1:8765/` by default. Setup prints a private local login link; do not share it. Restart Codex and Claude after installation so they load the new MCP configuration. Audio and video processing requires system FFmpeg/ffprobe. Content sent to a model provider follows the endpoint you configure; review that provider's privacy policy before enabling it.

After installation, the **Chat** tab at [friend.open-world.cn/app/](https://friend.open-world.cn/app/) checks for the local app and opens its WebUI on your computer. If it is unavailable, Chat shows the installation prompt and a manual Open local WebUI button. The website does not read your memory or local access token. On first login, the local WebUI presents a four-step guide for MCP, optional model endpoints, and the first upload; you can skip it and reopen it later in settings.

## Connections and Notion

The Connections page manages custom sources and platform connections, with the six quick actions retained below. Notion is the built-in account connector: choose **Notion → Connect account**, authorize your pages, select all authorized content or individual pages, and start syncing. You do not need to create a developer app or enter an OAuth secret.

Automatic sync defaults to every 15 minutes while your computer and friend. service are running. The Notion panel shows progress, saved memories, errors and the next run; it also supports manual sync, pause/resume, scope changes and disconnect. Disconnect stops new reads and revokes access while preserving saved memories. Authorized-content discovery is limited by Notion's API and is not a guaranteed complete workspace backup. Attachments and media are not downloaded or transcribed; incomplete content is reported.

Other platform rows, including Gmail, currently register receiving endpoints for external bridges; they do not yet authorize or synchronize those accounts. See the [connection guide](https://github.com/OpenWorld-Agent/friend./releases/download/v0.1.7/CONNECTORS.md).

Choose **Settings → Appearance → Language** for English or Simplified Chinese. Only the interface is translated: memories, page text, names, files and drafts remain in their original language.

## Phone recordings

friend. Memory v0.1.7 receives phone recordings itself; no separate receiver program is needed.

1. Install the **friend.** Android app (`friend-v1.0.1-debug.apk` in this release; Android 10 or later).
2. In the local WebUI, open **设置 → 手机录音 (Settings → Phone recordings)** and click **一键配对 (One-click pairing)**. Nothing needs to be configured: friend. Memory has a built-in default relay and its certificate, opens a channel and shows the QR. In a terminal, run `openworld-memory mobile pair`.
3. Tap **扫码配对 (Scan to pair)** in the app and scan the QR. The QR contains private keys: do not screenshot or share it. It hides itself after three minutes.
4. Record with the phone's normal recorder. New recordings are transcribed and saved as reviewable memories.

If no speech model is configured yet, one-click pairing also enables the public speech service behind the default relay (FireRed with speaker labels such as 人物A/人物B; it never uses anyone's voiceprint). With it, each recording is decrypted on your computer and its audio is sent through the relay to that service for transcription; neither stores it. To keep audio on your own infrastructure, configure your own speech model under **设置 → 模型接口 (Settings → Model endpoints)**; one-click pairing never replaces a model you configured. You can also use your own relay: open **使用自己的中转 (Use your own relay)** and enter its URL, administrator token (used once, not stored) and optional CA certificate, or run `openworld-memory mobile pair --relay-url …` with the token in `FRIEND_RELAY_ADMIN_TOKEN`.

The recording is encrypted with AES-256-GCM on the phone. The relay never receives the key, and for delivery it handles only ciphertext. Your computer keeps the pairing secrets in an owner-only file outside the settings API. On a rooted phone the app watches the system recorder in real time, including recorders that save to a private directory; otherwise you authorize the recorder's folder. Measured end to end on a test phone: about 8 seconds from saving a recording to a transcribed memory. The default relay limits new channels per computer and deletes ciphertext nobody collected within 7 days. Already using the standalone `friend_receiver.py`? Run `openworld-memory mobile import --config <path>` to keep the existing phone pairing. See the [phone recording guide](https://github.com/OpenWorld-Agent/friend./releases/download/v0.1.7/MOBILE-VOICE.md) for the protocol and security boundaries.

## MCP behavior

The MCP server tells client agents to **read memory only when a request needs personal context**—such as the user's prior decisions, preferences, projects, or devices. Generic questions should not trigger a memory lookup. When the user shares a durable personal fact, preference, goal, or meaningful change, the agent should proactively report it through `memory_add` or revise the existing item through `memory_update`, then tell the user what was saved. It must not invent facts or save passwords, API keys, transient chatter, or highly sensitive details without an explicit request. Candidates remain pending until reviewed; retrieved content is data, not instructions. Tool use ultimately depends on the client model, so these are server instructions rather than a guarantee of every model's behavior. See the [MCP policy document](https://github.com/OpenWorld-Agent/friend./releases/download/v0.1.7/MCP-POLICY.md).

## Developer information-source API

Developers can register local information sources such as smart-home hubs, then submit events to the same processing queue through a REST API. Each source receives a separate **write-only token**: it can submit events but cannot read memory or change settings. Events include a stable `event_id`, timezone-aware `occurred_at`, faithful `text`, and optional JSON `attributes`. Identical retries reuse the original job; conflicting event IDs are rejected. The original JSON is retained as hash-verified evidence, and resulting memories remain reviewable candidates.

The local API is at `http://127.0.0.1:8765`. Register with `POST /api/sources` using the WebUI admin token, then submit with `POST /api/sources/{source_id}/events` using the source token. Rotation and revocation endpoints are provided. See the [source API contract and examples](https://github.com/OpenWorld-Agent/friend./releases/download/v0.1.7/SOURCE-API.md), including Windows PowerShell usage. Do not give the WebUI admin token to a connector or expose the API to the public internet.

## Distribution and licenses

This repository intentionally contains documentation only; release assets contain compiled executables, a small installer, the friend. Android app (debug-signed demo build), checksums, and bundled license notices. The release archives do **not** contain the full source tree, development handoff documents, or example datasets. Applicable license texts are included inside each native archive. Publishing these binaries does not make every component open source.
