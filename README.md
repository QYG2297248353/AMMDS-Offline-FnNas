# AMMDS 飞牛 OS 离线应用包

## 项目概述

本项目是 [AMMDS](https://ammds.lifebus.top/)（Adult Movie MetaData Scraper）在飞牛 OS（FNOS）平台上的离线应用打包工程。AMMDS 是一款私人影视库管理平台，提供智能刮削元数据、演员识别、多源数据匹配等功能，帮助用户打造专属的家庭影院体验。

本项目通过 [FnPack](https://static2.fnnas.com/fnpack/) 工具将 AMMDS 后端二进制文件打包为 `.fpk` 安装包，适配飞牛 OS 的 Trim 应用框架，实现一键安装、配置、升级和卸载。

| 项目 | 详情 |
|------|------|
| 应用名 | ammds |
| 显示名 | AMMDS |
| 版本 | 1.6.58 |
| 支持架构 | amd64、arm64 |
| 最低系统版本 | FNOS 1.1.3100 |
| 维护者 | 萌森工作室 |
| 项目仓库 | [AMMDS-Docker](https://github.com/QYG2297248353/AMMDS-Docker) |
| 官方网站 | [https://ammds.lifebus.top/](https://ammds.lifebus.top/) |

## 功能特性

- **智能刮削元数据**：自动识别影视文件并匹配元数据信息
- **演员识别**：智能识别演员信息
- **多源数据匹配**：支持从多个数据源聚合和匹配影片信息
- **个人影视库管理**：集中管理个人影视收藏
- **定时任务**：支持自动化定时刮削任务
- **监控功能**：实时监控文件变化并自动处理

## 目录结构

```
AMMDS-Offline-FnNas/
├── .github/
│   └── workflows/
│       └── release-fnpack.yml     # GitHub Actions 构建工作流
├── app/
│   ├── bin/
│   │   └── .gitkeep               # 构建时放入 ammds-amd / ammds-arm 二进制文件
│   └── ui/
│       ├── config                  # 桌面启动器配置
│       └── images/                 # 应用图标
│           ├── ICON_64.PNG
│           └── ICON_256.png
├── cmd/                           # 生命周期脚本（Bash）
│   ├── main                        # 主程序控制脚本（start / stop / status）
│   ├── install_init                # 安装初始化：密码校验、端口检查、环境变量写入
│   ├── install_callback            # 安装结束回调
│   ├── uninstall_init              # 卸载初始化
│   ├── uninstall_callback          # 卸载回调：根据用户选择保留或删除数据
│   ├── upgrade_init                # 升级初始化
│   ├── upgrade_callback            # 升级结束回调
│   ├── config_init                 # 配置初始化：更新运行时配置
│   └── config_callback             # 配置结束回调
├── config/
│   ├── resource                    # 数据共享目录定义
│   └── privilege                   # 权限及用户组配置
├── wizard/                         # 安装向导页面配置
│   ├── install                     # 安装向导：管理员密码、端口、服务条款
│   ├── config                      # 配置向导：定时任务、监控、超时、文件大小
│   └── uninstall                   # 卸载向导：数据保留 / 删除选项
├── ICON.PNG                        # 应用图标
├── ICON_256.PNG                    # 应用图标
├── manifest                        # 应用清单文件（元数据）
├── LICENSE                         # 用户隐私保护协议
└── README.md                       # 本文档
```

## 安装与配置

### 安装向导

安装过程中需要完成以下配置：

**步骤一：管理员配置**

| 配置项 | 说明 |
|--------|------|
| 管理员密码 | 设置管理员密码（不少于 8 位） |
| 确认密码 | 再次输入密码确认 |
| 默认用户名 | ammds（无需输入，系统预设） |

**步骤二：应用配置**

| 配置项 | 说明 | 默认值 |
|--------|------|--------|
| 应用端口 | 应用 Web 服务端口号 | 9523 |
| 服务条款 | 需阅读并同意《用户隐私保护协议》后方可继续 | — |

### 配置向导

安装后可随时调整以下运行时配置：

| 配置项 | 说明 | 默认值 |
|--------|------|--------|
| 定时任务 | 是否启用定时刮削任务 | 启用 |
| 监控功能 | 是否启用文件变化监控 | 启用 |
| 网络超时 | 网络请求超时时间（秒） | 60 |
| 最大文件大小 | 单文件大小上限（支持 B/KB/MB/GB/TB） | 5GB |
| 最大请求大小 | 单次请求大小上限（支持 B/KB/MB/GB/TB） | 20GB |

## 启动与停止

```bash
# 启动服务
./cmd/main start

# 停止服务
./cmd/main stop

# 查看服务状态
./cmd/main status
```

## 卸载

卸载时需选择数据保留方式：

| 选项 | 说明 |
|------|------|
| 保留数据（推荐） | 保留所有应用数据，重新安装时可恢复 |
| 删除所有数据 | 永久删除所有数据，**此操作不可恢复** |

## 访问信息

安装完成后，通过浏览器访问应用：

- **地址**：`http://<NAS_IP>:<端口>`
- **默认端口**：9523
- **用户名**：ammds
- **密码**：安装时设置的管理员密码

## 构建说明

本项目通过 GitHub Actions 自动构建。当推送 `vX.Y.Z` 格式的 tag 并发布 Release 时，CI 工作流会自动：

1. 从 [AMMDS-Docker](https://github.com/QYG2297248353/AMMDS-Docker) 仓库下载 amd64 和 arm64 后端二进制文件
2. 根据 tag 更新 manifest 中的版本号
3. 使用 fnpack 工具构建 `.fpk` 安装包
4. 将构建产物上传至 GitHub Release

## 隐私保护

本应用遵循《用户隐私保护协议》，详细信息请参阅 [LICENSE](LICENSE) 文件。

## 技术支持

如有任何问题，请联系萌森工作室：

- 官方网站：[https://ammds.lifebus.top/](https://ammds.lifebus.top/)
- 技术支持邮箱：support@lifebus.top
- 服务时间：周一至周五 9:00-18:00（节假日除外）

## 版权信息

版权所有 © 2023-2025 新疆萌森软件开发工作室

统一社会信用代码：91652301MACHE3T54
