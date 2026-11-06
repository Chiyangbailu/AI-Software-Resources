# CONTENTS — Codex 一站式问题处理包

## 一、项目简介

本资料包是一套面向 Windows 用户的 ChatGPT / Codex 桌面客户端安装辅助工具。

主要解决 Windows 用户在安装 ChatGPT 桌面客户端时遇到的安装资源获取、离线部署、系统权限、安装验证及环境检查等问题。

本工具包采用 BAT + PowerShell + MSIX 的组合方式，将离线安装资源、自动化安装脚本和辅助工具整合在一起。

**核心目标：让用户通过简单的操作完成 Windows 桌面客户端的离线部署。**

注意：本工具负责本地客户端安装和部署，不保证在线服务、账号登录或 Codex 功能在所有网络环境下均可使用。

---

## 二、完整文件结构

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
│
├── README_CN.txt
└── 必用网站.txt
```

---

## 三、核心安装资源

### 1. payload 文件夹

该目录存放离线安装所需的核心资源。

| 文件 | 功能 |
|---|---|
| ChatGPT.msix | Windows 桌面客户端 MSIX 安装包 |
| ChatGPT-License.xml | 配套离线部署许可证文件 |
| SHA256.json | 安装资源完整性校验记录 |

安装程序会检查 MSIX 包的内部清单、应用身份、处理器架构及签名文件。

同时会解析 XML 许可证。

如果 SHA256.json 存在，安装程序还会验证 MSIX 和 XML 文件的 SHA-256 值。

### 2. tools 文件夹

该目录存放安装引擎和辅助脚本。

| 文件 | 功能 |
|---|---|
| Install-DesktopOffline.ps1 | 执行 Windows 桌面客户端离线部署 |
| Check-Desktop.ps1 | 检查当前用户应用注册状态及系统预配置状态 |
| Common.ps1 | 提供公共函数、资源检查、MSIX 信息读取及 SHA-256 校验 |

这些脚本由 BAT 启动程序调用，普通用户不需要单独运行。

---

## 四、一键安装入口

### 01_INSTALL_GUI_OFFLINE.bat

这是整个工具包的主要安装入口。

用户双击即可启动离线安装流程。

执行过程：

1. 自动定位工具包所在目录。
2. 调用 PowerShell 安装引擎。
3. 检查系统及安装资源。
4. 必要时自动请求 Windows 管理员授权。
5. 验证 MSIX 和配套 XML。
6. 验证 SHA-256（如果校验文件存在）。
7. 调用 Windows AppX 部署服务执行离线部署。
8. 检查部署结果并显示状态。

安装程序不访问网络，也不会修改 CC-Switch 的配置。

如果部署成功但应用尚未出现在当前用户环境中，按照提示注销并重新登录 Windows。

### 02_CHECK_GUI_INSTALL.bat

这是安装后的独立检查入口。

双击运行后，会调用 Check-Desktop.ps1。

主要检查：

- 当前 Windows 用户是否注册了 OpenAI.Codex 应用。
- 如果没有检测到 Codex，继续检查 OpenAI.ChatGPT。
- Windows 系统是否已经预配置相应应用。
- 是否存在安装状态异常。

完整检查系统预配置状态时，建议以管理员身份运行。

---

## 五、PowerShell 核心组件说明

### Install-DesktopOffline.ps1

负责实际执行离线部署。

主要功能：

- 检查 Windows 是否为 64 位系统。
- 检查 payload 核心文件。
- 自动申请管理员权限。
- 调用公共资源检查函数。
- 使用 Add-AppxProvisionedPackage 执行 MSIX 和离线许可证部署。
- 再次检查 Windows 系统预配置结果。
- 检查当前用户是否已经注册应用。

### Check-Desktop.ps1

负责安装状态诊断。

通过 Windows AppX 包管理接口读取应用注册信息及系统预配置信息，并根据检查结果返回状态代码。

### Common.ps1

公共底层函数库。

负责：

- 定位工具包根目录。
- 检查 MSIX 和许可证文件。
- 检查 MSIX 文件大小。
- 读取 MSIX 内部 AppxManifest.xml。
- 检查 AppxSignature.p7x 是否存在。
- 检查应用身份及 x64 架构。
- 验证 XML 文件格式。
- 读取 SHA256.json 并进行文件完整性校验。

---

## 六、配套软件

### CC-Switch

文件：

`CC-Switch-v4.0.17-Windows.msi`

提供 CC-Switch 的 Windows 安装程序。

它是独立配套软件，不属于主离线安装脚本的执行流程。

主安装程序不会修改 CC-Switch 配置。

### ChatGPT 在线安装器

文件：

`ChatGPT Installer.exe`

作为在线下载安装备用入口。

需要根据安装器自身的运行要求使用网络。

该程序不由主离线 BAT 自动调用。

### Google Chrome

文件：

`ChromeStandaloneSetup64.exe`

Google Chrome Windows 64 位安装程序。

作为配套浏览器资源单独使用。

---

## 七、其他说明文件

| 文件 | 用途 |
|---|---|
| README_CN.txt | 原始中文安装说明 |
| 必用网站.txt | 相关网站及资源入口汇总 |

建议首次使用前阅读中文说明文件。

---

## 八、适用范围及功能边界

本工具包主要用于：

- Windows x64 环境下的桌面客户端离线部署。
- MSIX 安装资源检查。
- 离线许可证文件检查。
- SHA-256 完整性验证。
- Windows 应用注册及预配置状态检查。

本工具包不负责：

- 安装 Codex CLI。
- 自动创建或提供 OpenAI 账号。
- 自动获得在线服务使用权限。
- 保证所有网络环境均能访问在线服务。
- 自动配置 CC-Switch。
- 自动修改用户的 Codex 配置及认证文件。

---

## 九、版权及第三方资源

本资料包可能包含第三方软件、安装文件及相关资源。

所有第三方内容均应遵循原始许可证和授权协议。

具体版权及授权说明请参阅仓库根目录的 NOTICE.md。

---

**项目名称：Codex 一站式问题处理包**

**发行版本：V1.0.0**

