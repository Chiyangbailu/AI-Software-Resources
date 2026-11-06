# INSTALL — Codex Windows 离线安装指南

本文档按 V1.0.0 发行包中实际的 BAT 和 PowerShell 脚本编写。请先完整阅读，再运行安装入口。

## 一、安装包说明

本资料包用于辅助 Windows x64 用户离线部署 ChatGPT Windows 桌面客户端，并在桌面客户端中使用其 Codex 功能。

主要资源：

- ChatGPT Windows 桌面客户端 MSIX。
- 配套离线许可证 XML。
- BAT 一键部署入口。
- PowerShell 安装和诊断脚本。
- 可选 SHA-256 文件完整性校验记录。
- ChatGPT 在线安装备用程序。
- CC-Switch 和 Google Chrome 独立安装资源。

**离线的含义：** 主安装流程使用本地 MSIX 和许可证文件，不会为了下载这两个部署输入而访问网络。安装后登录账号和使用在线服务仍需要满足相应的账号、网络和服务条件。

本项目为独立资料整理项目，非 OpenAI 官方项目，不代表 OpenAI 官方授权或背书。

---

## 二、运行环境与准备

### 运行环境

- Windows 64 位操作系统（当前脚本会检查是否为 64 位）。
- 系统具备可用的 AppX/MSIX 部署能力。
- 当前 Windows 用户能够在 UAC 提示时批准必要的管理员权限请求。
- 足够的磁盘空间。
- 完整下载并解压的 V1.0.0 ZIP 资料包。

脚本不会完整检测所有 Windows 版本、系统组件、组策略或组织管理策略的兼容性。若设备受到组织策略管理，部署仍可能被系统策略阻止。

### 下载入口

请从项目的 GitHub Release 下载正式发行包：

[Codex 一站式资料包 V1.0.0 — Release 页面](https://github.com/Chiyangbailu/AI-Software-Resources/releases/tag/v1.0.0)

在 Release 页面 Assets 区域下载完整 ZIP 文件。不要用 GitHub 自动生成的 Source code ZIP 代替正式发行资产。

---

## 三、第一步：完整解压

1. 下载完整 ZIP。
2. 使用 Windows 自带解压功能或 7-Zip 解压到本地磁盘。
3. 不要直接在压缩包预览窗口中运行 BAT。
4. 不要只复制 BAT 或 PowerShell 脚本。
5. 保留资料包原有的目录层级和文件名。

解压后，关键结构应为：

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

请重点确认：

- `payload\ChatGPT.msix` 存在。
- `payload\ChatGPT-License.xml` 存在。
- `tools\Install-DesktopOffline.ps1` 存在。
- `tools\Common.ps1` 存在。
- `01_INSTALL_GUI_OFFLINE.bat` 位于资料包根目录。

如果缺少 MSIX 或许可证文件，请重新下载完整发行包，不要尝试只运行脚本。

---

## 四、第二步：运行一键离线安装

在资料包根目录双击：

`01_INSTALL_GUI_OFFLINE.bat`

该 BAT 会调用 Windows 自带的 `powershell.exe`，再运行 `tools\Install-DesktopOffline.ps1`。

### 安装脚本实际执行步骤

1. 检查操作系统是否为 64 位。
2. 检查 MSIX 和离线许可证是否存在。
3. 检查当前进程是否具有管理员权限。
4. 如果当前不是管理员，脚本会通过 Windows UAC 请求提升权限，并等待提升后的安装进程结束。
5. 检查 MSIX 文件大小、内部清单、签名文件、Identity 和 x64 架构。
6. 解析离线许可证 XML。
7. 如果存在 `SHA256.json`，校验 MSIX 和许可证文件的 SHA-256。
8. 调用 Windows `Add-AppxProvisionedPackage` 部署 MSIX 和离线许可证。
9. 查询系统预配置列表，确认目标应用是否已预配置。
10. 查询当前用户应用注册状态，并显示下一步提示。

### 关于管理员权限

安装脚本已经包含自动提权逻辑：

- 如果当前进程已经具有管理员权限，继续执行。
- 如果当前进程没有管理员权限，会尝试通过 UAC 启动提升权限的 PowerShell 实例。
- 如果用户取消 UAC，或 Windows 无法启动提升权限的进程，安装不能继续完成系统级部署。

出现 UAC 提示时，请确认这是你主动启动的安装程序，再根据 Windows 提示批准或输入管理员凭据。

**不需要为了运行安装入口，预先把 BAT 设置成“始终以管理员身份运行”。** 当前脚本会在检测到权限不足时自行请求提升权限。

### 安装成功标志

脚本成功查询到匹配的系统预配置包后，会显示类似：

```text
[SUCCESS] OpenAI desktop app provisioned: ...
```

这表示系统级预配置已完成。它不一定表示当前登录用户已经完成应用注册。

如果当前用户尚未注册，脚本会提示：

```text
[NEXT STEP] Sign out of Windows and sign back in to register the GUI for your account.
```

请保存其他工作，注销 Windows 并重新登录，再从开始菜单启动 ChatGPT。

---

## 五、第三步：启动 ChatGPT 并进入 Codex

系统预配置成功后：

1. 如果安装脚本提示需要注销，先保存工作并注销 Windows。
2. 重新登录当前 Windows 账户。
3. 打开 Windows 开始菜单。
4. 找到并启动 ChatGPT 桌面应用。
5. 在应用左上角菜单中选择 Codex。

本资料包只负责本地桌面应用部署，不创建 OpenAI 账号、不提供账号资格，也不保证在线服务在所有地区或网络环境中均可访问。

---

## 六、第四步：检查安装状态

在资料包根目录双击：

`02_CHECK_GUI_INSTALL.bat`

检查入口会运行 `tools\Check-Desktop.ps1`。它不会安装应用，也不会自动请求管理员权限。

### 检查内容

脚本依次检查：

- Windows 版本和系统架构。
- 当前用户是否注册 `OpenAI.Codex`。
- 如果没有找到，再检查 `OpenAI.ChatGPT`。
- 系统预配置列表中是否存在 `OpenAI.Codex` 或 `OpenAI.ChatGPT`。

### 关于检查权限

当前用户注册状态可以通过当前用户范围的 AppX 查询读取。系统预配置状态查询使用 `Get-AppxProvisionedPackage -Online`。

如果检查时出现 `[DIAGNOSTIC ERROR]`，可能是系统预配置查询权限不足或其他诊断异常。此时可以：

1. 关闭检查窗口。
2. 右键 `02_CHECK_GUI_INSTALL.bat`。
3. 选择“以管理员身份运行”。
4. 再次检查。

这里的管理员启动由用户主动选择；检查脚本本身不会弹出 UAC。

### 退出代码

| 退出代码 | 含义 |
|---|---|
| `0` | 当前用户注册状态或系统预配置状态至少有一项检测到目标应用 |
| `1` | 当前用户注册状态和系统预配置状态均未检测到目标应用 |
| `2` | 诊断过程中发生异常 |

注意：退出代码 `0` 表示至少一项检查检测到目标应用，并不单独保证当前用户已经注册应用。请同时阅读窗口中显示的两类状态。

---

## 七、可选配套软件

以下程序不属于主离线安装 BAT 的自动执行流程。需要时可单独运行：

### 1. CC-Switch

文件：

`CC-Switch-v4.0.17-Windows.msi`

按需单独安装。主离线安装脚本不会自动安装 CC-Switch，也不会修改它的配置。

### 2. ChatGPT 在线安装器

文件：

`ChatGPT Installer.exe`

作为在线安装备用入口。使用时需要网络连接，具体行为以安装器自身界面为准。如果离线部署已成功且满足使用需求，不需要再运行此备用安装器。

### 3. Google Chrome

文件：

`ChromeStandaloneSetup64.exe`

Google Chrome Windows x64 安装程序，按需单独安装。

---

## 八、常见问题排查

### 问题 1：提示找不到 MSIX

检查：

```text
payload\ChatGPT.msix
```

请确认下载的是完整发行包，且没有破坏目录结构。

### 问题 2：提示找不到离线许可证

检查：

```text
payload\ChatGPT-License.xml
```

请确认文件名完全一致、文件没有被改名，且下载内容确实是 XML 文件，而不是网页或文本错误页。

### 问题 3：MSIX 文件过小或资源校验失败

当前代码要求 MSIX 文件至少为 20,000,000 字节，并检查内部清单、签名文件、Identity 和架构。

若提供了 `SHA256.json`，还会检查 MSIX 和许可证 XML 的哈希。出现哈希不一致、记录缺失或格式错误时，脚本会停止。

请重新下载完整且可信的发行包。不要通过修改哈希记录来掩盖文件差异。

### 问题 4：提示 MSIX Identity 或架构不符合预期

当前安装脚本接受的 Identity 名称为：

- `OpenAI.Codex`
- `OpenAI.ChatGPT`

并要求 `ProcessorArchitecture` 为 `x64`。如果资源不符合这些条件，脚本会停止，避免继续部署不匹配的包。

### 问题 5：UAC 提示被取消或管理员权限请求失败

重新双击 `01_INSTALL_GUI_OFFLINE.bat`，在确认提示来自本次主动启动的安装程序后，按需批准 UAC 请求。

如果设备由组织管理，管理员策略也可能阻止系统级部署。请联系设备管理员，不要尝试绕过组织策略。

### 问题 6：显示预配置成功，但开始菜单暂时没有应用

系统级预配置与当前用户注册是两个不同状态。

如果脚本提示：

```text
Sign out and sign back in
```

请保存工作、注销 Windows 并重新登录，然后再检查开始菜单和应用注册状态。

### 问题 7：检查程序出现诊断错误

如果看到：

```text
[DIAGNOSTIC ERROR]
```

可以右键 `02_CHECK_GUI_INSTALL.bat`，选择“以管理员身份运行”后再检查。检查程序不会自行提权。

### 问题 8：Windows AppX 部署失败

可能涉及：

- Windows 版本、系统组件或组织策略兼容性。
- AppX Deployment Service 状态。
- 已安装版本或包身份冲突。
- MSIX 签名验证。
- 离线许可证验证。

请保留完整错误输出、Windows 版本和系统架构，再提交问题反馈。不要在不了解原因时删除系统应用包或修改部署服务配置。

---

## 九、隐私与配置保护范围

当前安装脚本明确不会修改：

```text
.codex\config.toml
.codex\auth.json
```

也不会自动配置 CC-Switch。它的主要工作是验证本地部署资源并调用 Windows AppX 部署服务。

---

## 十、重要声明

1. 本工具包用于 Windows 桌面客户端离线部署，不是 Codex CLI 安装包。
2. 安装成功不代表账号自动获得在线服务使用权限。
3. 在线功能仍受账号、地区、网络和服务政策约束。
4. 安装脚本只对其明确检查的系统架构和资源项目作出判断，不保证所有 Windows 环境均兼容。
5. 不要随意修改 `payload` 和 `tools` 中的文件。
6. 第三方软件和资源遵循各自的许可证及授权条件；请参阅仓库根目录的 [NOTICE.md](NOTICE.md)。

---

**项目：Codex 一站式资料包**  
**版本：V1.0.0**
