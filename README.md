# Codex 中文资料中心

**面向 Windows 用户的 ChatGPT / Codex 桌面客户端安装辅助工具及相关资料发布中心。**

本项目整理并发布 Windows 桌面客户端离线部署资源、自动化安装脚本、安装检查工具及相关使用说明，帮助用户更方便地完成客户端安装和本地环境部署。

本项目为独立资料整理与发布项目，非 OpenAI 官方项目，不代表 OpenAI 官方立场，也不构成官方授权或背书。

---

## 📦 最新发行版本

### Codex 一站式问题处理包 V1.0.0

<escape> </escape>

| 项目 | 信息 |
|---|---|
| 版本 | V1.0.0 |
| 文件格式 | ZIP |
| 文件大小 | 约 1.15GB |
| 平台 | Windows x64 |
| 安装方式 | MSIX 离线部署 |
| 发布状态 | 已发布 |

**[进入 V1.0.0 正式发行页面](https://github.com/Chiyangbailu/AI-Software-Resources/releases/tag/v1.0.0)**

进入发行页面后，在 Assets 区域选择资料包下载。

---

## 🎯 项目用途

本工具包主要解决 Windows 用户安装 ChatGPT 桌面客户端时遇到的部署问题。

主要功能：

- Windows 桌面客户端 MSIX 离线安装。
- 自动化 PowerShell 安装流程。
- Windows 管理员权限请求。
- 离线许可证文件检查。
- MSIX 内部身份和架构检查。
- SHA-256 文件完整性验证。
- Windows 应用注册状态检查。
- 安装后的系统预配置状态诊断。

本工具包的核心特点是将离线安装资源和自动化脚本整合在一起，用户无需手动逐条执行 PowerShell 命令。

---

## 🧰 工具包内容

```text
Codex_一站式问题处理包/
│
├── payload/
│   ├── ChatGPT.msix
│   ├── ChatGPT-License.xml
│   └── SHA256.json
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

### 核心入口

| 文件 | 用途 |
|---|---|
| 01_INSTALL_GUI_OFFLINE.bat | 一键启动离线安装 |
| 02_CHECK_GUI_INSTALL.bat | 检查安装及系统预配置状态 |

### 核心脚本

| 文件 | 用途 |
|---|---|
| Install-DesktopOffline.ps1 | 离线部署执行引擎 |
| Check-Desktop.ps1 | 应用安装状态检查 |
| Common.ps1 | 公共检查、资源验证及校验函数 |

详细文件说明请参阅：

[CONTENTS.md](CONTENTS.md)

---

## 🚀 快速安装

1. 进入 [Releases](https://github.com/Chiyangbailu/AI-Software-Resources/releases) 页面。
2. 下载最新完整 ZIP 资料包。
3. 将压缩包完整解压到本地目录。
4. 阅读压缩包内的中文说明。
5. 双击 `01_INSTALL_GUI_OFFLINE.bat`。
6. 根据 Windows 提示完成必要的管理员授权。
7. 等待部署成功。
8. 如有提示，注销并重新登录 Windows。
9. 从开始菜单启动 ChatGPT，在应用左上角菜单中选择 Codex。

安装后的状态检查：

运行：

`02_CHECK_GUI_INSTALL.bat`

建议以管理员身份运行，以便完整检查系统预配置状态。

**完整安装步骤：**

[查看 INSTALL.md](INSTALL.md)

---

## 🖥️ 适用环境

- Windows 64 位操作系统。
- 支持 AppX/MSIX 部署的 Windows 环境。
- 具有必要的管理员授权。

安装脚本会检查系统是否为 64 位，但不会自动判断所有 Windows 版本的兼容性。

---

## 🔐 完整性与安全

本项目提供 SHA256.json，用于验证核心离线安装文件的完整性。

安装过程中会检查：

- MSIX 文件是否存在。
- MSIX 文件大小是否合理。
- MSIX 内部清单是否存在。
- MSIX 签名文件是否存在。
- 应用身份是否符合预期。
- 应用架构是否为 x64。
- XML 文件格式是否有效。
- SHA-256 是否匹配（存在校验文件时）。

如遇到校验失败，请不要忽略错误或强行安装。

---

## ⚠️ 功能边界

本工具包解决的是 Windows 桌面客户端的本地安装与部署问题。

它不负责：

- 安装 Codex CLI。
- 提供或创建 OpenAI 账号。
- 绕过账号资格、地区或网络限制。
- 保证在线 AI 服务在所有网络环境下均可访问。
- 自动配置 CC-Switch。
- 修改用户的 Codex 配置和认证文件。

离线部署成功不代表在线功能已经具备使用条件。

---

## 📚 项目文档

| 文档 | 内容 |
|---|---|
| [CONTENTS.md](CONTENTS.md) | 完整文件结构与功能说明 |
| [INSTALL.md](INSTALL.md) | 详细安装教程及故障排查 |
| [NOTICE.md](NOTICE.md) | 版权与第三方资源声明 |

---

## 📋 版本记录

### V1.0.0

首次公开发行。

- 整合 ChatGPT Windows 桌面客户端离线部署资源。
- 提供 BAT 一键安装入口。
- 提供 PowerShell 自动化部署脚本。
- 提供安装状态检查工具。
- 提供 SHA-256 完整性验证。
- 整合 CC-Switch、Chrome 等配套安装资源。

[查看全部发行版本](https://github.com/Chiyangbailu/AI-Software-Resources/releases)

---

## ⚖️ 版权及授权声明

本项目为独立整理和发布项目。

仓库及资料包中可能包含第三方软件、安装资源和相关文档。

第三方软件及资源的版权、使用权限和再分发条件均以原始权利人的许可证及授权协议为准。

本仓库不因收录第三方资源而自动取得其所有权或再分发授权。

详细说明请参阅 [NOTICE.md](NOTICE.md)。

---

## 📮 问题反馈

如果在下载、解压、安装或使用过程中遇到问题，可以通过 GitHub Issues 提交反馈。

建议提供：

- 使用的资料版本。
- Windows 系统版本及架构。
- 安装过程中出现的错误信息。
- 相关截图或日志。

---

**持续整理、持续完善，让 Windows 用户更方便地获取和部署相关工具。**

