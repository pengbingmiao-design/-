# 浏览器多账号管理器

为每个用户启动独立的浏览器实例，每个实例使用独立 `--user-data-dir`，实现 Cookie / Session 完全隔离。

## 功能

- 一键启动多个浏览器实例，每个用户独立 Session
- 支持 Edge / Chrome / Brave 浏览器
- 多网址管理，切换站点不影响用户配置
- 机器码绑定激活，防止未授权使用
- 启动时自动检测更新

## 快速开始

1. 解压后双击 `BrowserProfileManager.exe`
2. 首次使用会显示激活弹窗，复制机器码发给卖家获取激活文件
3. 将 `license.key` 放入 exe 同目录，再次双击即可激活
4. 点击「新建用户」创建第一个用户，选择网址，点击启动

## 系统要求

- Windows 10 / 11（64位）
- Edge / Chrome 浏览器

## 目录结构

```
~/.browser_profile_manager/
├── config.json          # 配置文件
├── license.dat          # 激活状态
└── users/<uid>/         # 各用户浏览器数据
```
