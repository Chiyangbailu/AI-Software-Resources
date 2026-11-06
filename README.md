# Codex 中文资料中心

**面向 Windows 用户的 ChatGPT / Codex 桌面客户端离线部署辅助工具及资料发布中心。**

本项目整理并发布 Windows 桌面客户端离线部署资源、自动化安装脚本、安装状态检查工具及配套使用说明，帮助用户完成本地客户端部署。

本项目为独立资料整理与发布项目，**非 OpenAI 官方项目**，不代表 OpenAI 官方立场，也不构成 OpenAI 的授权或背书。

---

## 📦 当前正式发行版

### Codex 一站式资料包 V1.0.0

| 项目 | 信息 |
|---|---|
| 版本 | V1.0.0 |
| GitHub Release | `v1.0.0` |
| Release 资产文件名 | `Codex-.-v1.0.0.zip` |
| 文件格式 | ZIP |
| 文件大小 | 约 1.15 GB |
| 平台 | Windows x64 |
| 安装方式 | MSIX + 离线许可证部署 |
| 发布日期 | 2026-11-06 |

**[打开 V1.0.0 正式发行页面](https://github.com/Chiyangbailu/AI-Software-Resources/releases/tag/v1.0.0)**

请在发行页面的 **Assets** 区域下载完整 ZIP 资料包。不要只下载仓库源码压缩包来代替正式发行包。

发行资产 SHA-256：

```text
809cc276597f2c889160e72bc23606727b9e7f24b7397df54267525c352da146
```

---

## 🎯 项目用途

本工具包主要用于 Windows x64 环境下，通过离线 MSIX 和许可证文件部署 ChatGPT Windows 桌面客户端，并在客户端中使用其提供的 Codex 功能。

主要组成：

- ChatGPT Windows 桌面客户端 MSIX 离线安装资源。
- 配套离线许可证 XML。
- BAT 一键启动入口。
- PowerShell 离线部署脚本。
- MSIX 文件结构、应用身份及架构检查。
- 可选 SHA-256 完整性校验。
- Windows 用户应用注册状态和系统预配置状态检查。
- CC-Switch、ChatGPT 在线安装器及 Chrome 等独立配套资源。

本工具包的安装入口会调用 PowerShell 脚本完成检查和部署，不要求用户手动逐条输入安装命令。

---

## 🧰 资料包目录结构

以下为当前安装脚本所使用的目录和文件名。请完整解压，并保留相对路径：

```text
资料包根目录/
│
├── payload/
│   ├── ChatGPT.msix
│   ├── ChatGPT-License.xml
│   └── SHA256.json                 （如发行包提供）
│
├── tools/
│   ├── Check-Desktop.ps1
│   ├── Common.ps1
│   └── Install-DesktopOffline.ps1
│
├── 01_INSTALL_GUI_OFFLINE.bat
├── 02_CHECK_GUI_INSTALL.bat
│
├── CC-Switch-v4.0.17-Windows.msi
├── ChatGPT Installer.exe
├── ChromeStandaloneSetup64.exe
├── README_CN.txt
└── 必用网站.txt
```

### 两个操作入口

| 文件 | 用途 |
|---|---|
| `01_INSTALL_GUI_OFFLINE.bat` | 启动 ChatGPT 桌面客户端离线部署 |
| `02_CHECK_GUI_INSTALL.bat` | 检查当前用户应用注册状态和系统预配置状态 |

### 三个 PowerShell 组件

| 文件 | 用途 |
|---|---|
| `Install-DesktopOffline.ps1` | 检查环境、请求必要的管理员授权并执行离线部署 |
| `Check-Desktop.ps1` | 读取用户应用注册状态及系统预配置状态 |
| `Common.ps1` | 提供资源路径、MSIX/XML 检查和 SHA-256 校验函数 |

---

## 🚀 快速安装

1. 打开 [GitHub Releases](https://github.com/Chiyangbailu/AI-Software-Resources/releases)。
2. 进入 V1.0.0，下载 Assets 中的完整 ZIP 资料包。
3. 将 ZIP 完整解压到本地磁盘。
4. 确认解压目录中同时存在 `payload`、`tools` 和两个 BAT 文件。
5. 双击 `01_INSTALL_GUI_OFFLINE.bat`。
6. 如 Windows 弹出 UAC 提示，确认后允许本次安装程序请求管理员权限。
7. 等待脚本完成 MSIX 和离线许可证部署及部署结果检查。
8. 如果脚本提示当前用户尚未注册应用，保存工作后注销 Windows，再重新登录。
9. 从开始菜单启动 ChatGPT 桌面应用，在应用左上角菜单中选择 Codex。

安装后如需检查状态，双击：

`02_CHECK_GUI_INSTALL.bat`

该检查入口不会自动请求管理员权限。若检查系统预配置状态时出现权限诊断错误，可右键该 BAT，选择“以管理员身份运行”。

**详细步骤和故障排查：** [INSTALL.md](INSTALL.md)

---

## 🖥️ 运行环境与兼容性

- 目标平台：Windows x64。
- 需要 Windows 提供可用的 AppX/MSIX 部署能力。
- 离线部署属于系统级操作，需要管理员权限。
- 安装脚本会检查操作系统是否为 64 位；它不会完整检测每个 Windows 版本、系统组件或策略的兼容性。

两个 BAT 均显式调用 Windows 自带的 `powershell.exe`。本工具包没有要求用户另外安装 PowerShell 7。

---

## 🔐 文件检查与完整性校验

安装脚本在调用 Windows 部署服务前会检查：

- `payload\ChatGPT.msix` 和 `payload\ChatGPT-License.xml` 是否存在。
- MSIX 文件大小是否至少为 20,000,000 字节。
- MSIX 内部是否包含 `AppxManifest.xml` 和 `AppxSignature.p7x`。
- MSIX Identity 是否为 `OpenAI.Codex` 或 `OpenAI.ChatGPT`。
- MSIX 处理器架构是否为 `x64`。
- 离线许可证文件是否为可解析的 XML，而不是 HTML 页面。
- 如果存在 `SHA256.json`，是否与 MSIX 和许可证文件的 SHA-256 一致。

`SHA256.json` 在当前脚本中是可选校验文件：缺失时会给出警告，不会仅因该文件缺失而停止安装；如果文件存在但记录缺失、格式错误或哈希不匹配，安装会停止。

脚本会检查 MSIX 内部签名文件是否存在；实际 MSIX 签名验证由 Windows AppX 部署流程处理。文件哈希用于检查文件是否与随包记录一致，不代表对发布来源或第三方授权作出认证。

如出现校验失败，请停止安装，不要通过修改哈希记录来掩盖文件不一致。

---

## ⚠️ 功能边界

本工具包处理的是 Windows 桌面客户端本地部署，不提供在线服务或账号资格。

本工具包不负责：

- 安装 Codex CLI。
- 创建、提供或验证 OpenAI 账号。
- 绕过账号资格、地区、网络或服务访问限制。
- 保证在线 AI 服务在所有网络环境下均可访问。
- 自动安装或配置 CC-Switch。
- 自动安装 ChatGPT 在线安装器或 Google Chrome。
- 修改用户的 `.codex\config.toml` 或 `.codex\auth.json`。

离线部署成功不代表账号登录、在线服务或 Codex 功能在当前网络和账号条件下必然可用。

---

## 📚 项目文档

| 文档 | 内容 |
|---|---|
| [CONTENTS.md](CONTENTS.md) | 资料包目录结构、脚本职责和检查逻辑 |
| [INSTALL.md](INSTALL.md) | 下载、解压、安装、验证及故障排查 |
| [NOTICE.md](NOTICE.md) | 版权与第三方资源声明 |

---

## 📋 版本记录

### V1.0.0 — 首次公开发行

- 整合 ChatGPT Windows 桌面客户端离线部署资源。
- 提供 BAT 一键安装和安装后检查入口。
- 提供 PowerShell 部署及诊断脚本。
- 提供 MSIX 身份、架构、文件结构和 XML 检查。
- 支持使用 `SHA256.json` 对核心离线文件进行可选完整性校验。
- 整合 CC-Switch、ChatGPT 在线安装器和 Chrome 等独立配套资源。

[查看全部发行版本](https://github.com/Chiyangbailu/AI-Software-Resources/releases)

---

## ⚖️ 版权及第三方资源声明

本项目为独立整理和发布项目。仓库及资料包中可能包含第三方软件、安装文件和相关资料。

第三方软件及资源的版权、使用权限和再分发条件均以原始权利人的许可证及授权协议为准。本仓库不因收录第三方资源而自动取得其所有权或再分发授权。

详细说明请参阅 [NOTICE.md](NOTICE.md)。

---

## 📮 问题反馈

如果在下载、解压、安装或使用过程中遇到问题，可以通过 GitHub Issues 提交反馈。建议同时提供：

- 资料包版本及下载文件名。
- Windows 版本和系统架构。
- 使用的是安装入口还是检查入口。
- 完整错误信息、退出代码及相关截图。

---

**持续整理、持续完善，让 Windows 用户更方便地获取和部署相关工具。**
