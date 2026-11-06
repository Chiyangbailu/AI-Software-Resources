# CONTENTS — Codex 一站式资料包

本文档说明 V1.0.0 发行资料包的目录结构、文件用途和实际执行逻辑。内容以当前随包 BAT / PowerShell 源码为准；不代表本工具包会执行未由脚本实现的功能。

## 一、项目简介

本资料包面向 Windows x64 用户，提供 ChatGPT Windows 桌面客户端的离线部署辅助工具，以及安装后状态检查工具。

核心技术组成：

- BAT：提供双击启动入口。
- Windows PowerShell：执行资源检查、UAC 提权、部署和诊断。
- MSIX + 离线许可证 XML：作为桌面客户端的离线部署输入。
- Windows AppX 部署服务：执行系统级应用预配置。

本工具包用于部署桌面 GUI 客户端，不是 Codex CLI 安装包，也不是 OpenAI 官方发布的安装程序。

**重要边界：** 本地离线部署成功不等于 OpenAI 账号、网络访问或在线服务资格已经就绪。

---

## 二、发行资料包目录

以下是当前文档所描述的发行结构。目录名称可以因解压工具或用户选择而不同；请以解压后的实际根目录为准。关键是保留相对目录和脚本引用的文件名。

```text
资料包根目录/
│
├── payload/
│   ├── ChatGPT.msix
│   ├── ChatGPT-License.xml
│   └── SHA256.json                 （可选；存在时执行哈希校验）
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

### 路径约束

- 两个 BAT 文件位于资料包根目录。
- `payload` 和 `tools` 文件夹也必须位于资料包根目录。
- 安装 BAT 调用 `tools\Install-DesktopOffline.ps1`。
- 检查 BAT 调用 `tools\Check-Desktop.ps1`。
- 安装脚本从其父目录定位资料包根目录，再定位 `payload`。
- `Common.ps1` 与安装脚本位于同一 `tools` 目录。

不要把 BAT 或 PowerShell 脚本单独移出原目录，也不要只复制脚本而遗漏离线安装资源。

---

## 三、核心资源：payload

| 文件 | 用途 | 当前代码行为 |
|---|---|---|
| `ChatGPT.msix` | Windows 桌面客户端 MSIX 安装包 | 必须存在；文件大小必须至少为 20,000,000 字节 |
| `ChatGPT-License.xml` | 离线部署许可证 | 必须存在；必须能作为 XML 解析，不能是 HTML 页面 |
| `SHA256.json` | 核心离线文件的哈希记录 | 可选；存在时必须包含两个指定文件名对应的 64 位十六进制 SHA-256 |

### MSIX 检查项目

`Common.ps1` 会把 MSIX 作为 ZIP 兼容容器打开，并检查：

1. 是否存在 `AppxManifest.xml`。
2. 是否存在 `AppxSignature.p7x`。
3. 是否能从清单中读取 `Identity`。
4. Identity 名称是否为 `OpenAI.Codex` 或 `OpenAI.ChatGPT`。
5. `ProcessorArchitecture` 是否为 `x64`。

脚本会检查签名文件是否存在；MSIX 的实际签名验证由 Windows AppX 部署流程执行。不能把“签名文件存在”误解为脚本已独立完成全部证书链验证。

### SHA-256 行为

如果 `SHA256.json` 存在，脚本按以下属性名读取期望值：

- `ChatGPT.msix`
- `ChatGPT-License.xml`

两项均须为 64 位十六进制字符串，并且必须与实际文件计算出的 SHA-256 一致。缺少记录、记录格式错误或哈希不匹配都会导致检查失败。

如果 `SHA256.json` 不存在，当前代码只发出警告，不会仅因该文件缺失而停止安装。因此文档不把该校验描述为无条件强制门禁。

---

## 四、BAT 启动入口

### 1. `01_INSTALL_GUI_OFFLINE.bat`

**用途：** 启动桌面客户端离线部署。

实际执行方式：

- 切换到 BAT 所在目录。
- 调用 Windows 自带的 `powershell.exe`。
- 使用 `-NoProfile -ExecutionPolicy Bypass` 启动 `tools\Install-DesktopOffline.ps1`。
- 保存并显示 PowerShell 返回的退出代码。
- 根据退出代码显示部署成功或失败提示，并暂停窗口供用户查看。

此入口不会自动运行 CC-Switch MSI、ChatGPT 在线安装器或 Chrome 安装程序。

### 2. `02_CHECK_GUI_INSTALL.bat`

**用途：** 启动独立安装状态诊断。

实际执行方式：

- 切换到 BAT 所在目录。
- 调用 Windows 自带的 `powershell.exe`。
- 使用 `-NoProfile -ExecutionPolicy Bypass` 启动 `tools\Check-Desktop.ps1`。
- 保留诊断脚本退出代码并暂停窗口。

**权限区别：** 该检查入口不会自动请求管理员权限。若需要可靠读取系统级预配置状态，可由用户主动右键选择“以管理员身份运行”。

---

## 五、PowerShell 组件

### 1. `Install-DesktopOffline.ps1`

这是离线部署执行引擎。当前源码实现的主要步骤：

1. 加载同目录的 `Common.ps1`。
2. 定位资料包根目录及 `payload`。
3. 检查 Windows 是否为 64 位操作系统。
4. 检查 MSIX 和许可证 XML 是否存在。
5. 检查当前 PowerShell 是否处于管理员角色。
6. 如果不是管理员，使用 `Start-Process -Verb RunAs` 启动提升权限的 PowerShell 实例，并等待其退出。
7. 调用公共函数检查 MSIX、许可证和可选 SHA-256。
8. 调用 `Add-AppxProvisionedPackage -Online -PackagePath ... -LicensePath ... -Regions all` 执行系统级部署。
9. 查询系统预配置包，确认目标应用是否出现。
10. 检查当前用户是否已注册该应用，并给出后续提示。

如果系统预配置成功，但当前用户尚未注册应用，脚本会提示注销并重新登录 Windows。

### 2. `Check-Desktop.ps1`

这是独立诊断脚本，不负责安装或修复。

当前源码会：

- 读取 Windows 版本和操作系统架构。
- 先检查当前用户是否注册 `OpenAI.Codex`。
- 若未找到，再检查 `OpenAI.ChatGPT`。
- 查询系统预配置列表中的 `OpenAI.Codex` 或 `OpenAI.ChatGPT`。
- 显示找到的应用信息，或提示未找到。
- 根据诊断结果返回退出代码。

退出代码：

| 代码 | 含义 |
|---|---|
| `0` | 当前用户注册状态或系统预配置状态至少有一项检测到目标应用 |
| `1` | 两项检查均未检测到目标应用 |
| `2` | 诊断过程中发生异常 |

读取系统预配置状态时如果权限不足，脚本会进入异常处理并返回 `2`。脚本不会自行弹出 UAC；需要时由用户主动以管理员身份重新运行检查入口。

### 3. `Common.ps1`

公共函数库，当前为安装脚本提供：

- 资料包根目录定位。
- MSIX 与许可证文件存在性检查。
- MSIX 最小文件大小检查。
- MSIX 内部清单及签名文件存在性检查。
- 应用 Identity 与 x64 架构检查。
- 离线许可证 XML 解析。
- 可选 SHA-256 记录读取和校验。

该文件由安装脚本加载，不是面向普通用户的独立操作入口。

---

## 六、独立配套软件与资料

以下资源不属于两个 BAT 的自动执行流程，需要用户按需单独启动。

| 文件 | 用途 | 与主安装流程的关系 |
|---|---|---|
| `CC-Switch-v4.0.17-Windows.msi` | CC-Switch Windows 安装程序 | 不由主安装 BAT 自动调用；主安装脚本不修改其配置 |
| `ChatGPT Installer.exe` | ChatGPT 在线安装备用程序 | 不由主安装 BAT 自动调用；使用时按程序要求联网 |
| `ChromeStandaloneSetup64.exe` | Google Chrome Windows x64 安装程序 | 独立安装，不属于离线部署步骤 |
| `README_CN.txt` | 随包中文说明 | 供用户阅读 |
| `必用网站.txt` | 网站及资源入口汇总 | 供用户查阅 |

第三方软件的使用、安装和再分发应遵循各自的许可证及授权条件。请参阅仓库根目录的 [NOTICE.md](NOTICE.md)。

---

## 七、功能范围与不包含的操作

本资料包当前实现：

- Windows x64 架构检查。
- MSIX 和离线许可证资源检查。
- MSIX 内部 Identity、架构及关键条目检查。
- 可选 SHA-256 校验。
- Windows 系统级 MSIX 离线预配置。
- 当前用户注册状态和系统预配置状态诊断。

本资料包当前不实现：

- Codex CLI 安装。
- OpenAI 账号创建、验证或资格开通。
- 地区或网络限制绕过。
- 在线 AI 服务连通性检测。
- CC-Switch 自动安装或自动配置。
- 对 `.codex\config.toml`、`.codex\auth.json` 的修改。
- 对所有 Windows 版本和系统策略的完整兼容性预检。

---

## 八、文档索引

- [README.md](README.md)：项目介绍和快速入口。
- [INSTALL.md](INSTALL.md)：下载、解压、安装、检查及故障排查。
- [NOTICE.md](NOTICE.md)：版权与第三方资源声明。

---

**项目：Codex 一站式资料包**  
**发行版本：V1.0.0**
