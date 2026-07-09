# 浏览器多账号管理器

## 项目概述
tkinter GUI 工具，为每个"用户"启动独立的浏览器实例（Edge/Chrome），
每个实例使用独立的 `--user-data-dir`，实现 Cookie/Session 隔离。

## 核心文件
| 文件 | 作用 |
|------|------|
| main.py | 主程序，含 UI + 数据管理 + 激活弹窗 + 更新弹窗 |
| license.py | 激活模块：机器码生成、HMAC-SHA256(含机器码)签名验证、XOR混淆存储 |
| update.py | 更新模块：GitHub 版本检测、下载、自替换重启 |
| keygen.py | 卖家工具：`--file --mid <机器码>` 或交互式生成绑定机器的 license.key |
| version.json | 版本信息模板（提交到 GitHub 仓库） |
| 生成激活码.bat | 一键启动 keygen 交互模式 |
| run.bat | 开发测试用启动脚本 |

## 架构
- UI：纯 tkinter（无 ttk 主题依赖），PanedWindow 左右分栏
- 数据：JSON 存储在 `~/.browser_profile_manager/config.json`
- 用户数据：`~/.browser_profile_manager/users/<uid>/`
- 激活状态：`~/.browser_profile_manager/license.dat`（XOR混淆）

## 激活系统（v2 — 机器码绑定）

### 激活码格式
激活码(32位) = 随机16位 + HMAC-SHA256(随机16位 + 目标机器码, 内置密钥)[:16]
激活码在生成时即绑定目标机器码，换电脑签名验证直接失败。

### 机器码
SHA256(MAC地址 + 计算机名 + 用户名)[:16] → 固定16位十六进制，除非换网卡/重装系统永久不变。

### 客户侧流程
双击 exe → 未激活 → 弹窗显示机器码 → 客户复制发给卖家
→ 卖家生成 license.key → 客户放入 exe 同目录
→ 双击 exe → 自动激活 → license.key 被删除 → 进入主界面

### 卖家侧流程
双击 生成激活码.bat → 粘贴客户机器码 → 回车 → 生成 license.key
或：python keygen.py --file --mid <机器码>

## 自动更新系统

启动时通过 raw.githubusercontent.com 读取 version.json，版本号高于当前则弹窗。

- 立即更新 → 下载新 exe → 批处理替换 → 自动重启
- 稍后提醒 → 正常启动，下次再提示
- 网络异常 → 静默跳过，不影响使用
- GitHub 仓库需为公开（无需 token）

### 发布新版本流程
1. 修改 update.py 中的 APP_VERSION
2. 打包新 exe
3. GitHub Releases → 创建 tag → 上传 exe
4. 更新 version.json（版本号 + 下载链接 + 更新日志）→ commit & push
5. 所有已激活客户下次启动自动收到更新提示

## 入口流程
main() → root.withdraw() → is_activated()?
  ├─ 否 → _try_auto_activate() → 失败则 ActivationDialog
  └─ 是 → root.deiconify() → 更新检查 → 正常启动

## 打包
- Python: 3.10 (`C:\Users\Novice_Anony\AppData\Local\Programs\Python\Python310\python.exe`)
- PyInstaller: 6.21.0
- 命令：`pyinstaller --onefile --noconsole --hidden-import license --hidden-import update --icon=icon.ico --add-data "icon.ico;." --name BrowserProfileManager main.py`
- 输出：`dist\BrowserProfileManager.exe` (~9MB)

## 配色
C_PRIMARY=#1A73E8, C_DANGER=#D93025, C_BG=#EEF1F5, C_CARD=#FFFFFF
