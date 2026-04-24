简体中文 | [English](./README.md)

# Tencent Vod Player SDK Skills 使用指引

Tencent Vod Player SDK Skills 为腾讯云点播视频播放场景提供结构化指引。它可以将自然语言需求转化为可执行步骤，帮助 AI Agent 高效完成播放器 SDK 在 Android / iOS / Flutter / Web / HarmonyOS 平台的集成。

## 前置条件

使用 Skills 前，请确保您的 AI 编程工具已支持 Skills 功能。目前支持的工具包括：

- CodeBuddy

- Trae

- Cursor

- Codex

- Claude Code

- Claude Desktop


## 安装

### 方式一：手动下载后导入

[点击这里](https://mediacloud-76607.gzc.vod.tencent-cloud.com/aicoding/skills/tencent-vod-player-skills.zip) 下载Tencent Vod Player SDK Skills 安装 zip 包，解压后导入到 AI 编程工具的 Skills 存放目录。

### 方式二：通过 GitHub 克隆安装

可通过 GitHub 克隆：

``` bash
git clone https://github.com/LiteAVSDK/tencent-vod-player-skills.git ~/.skills/tencent-vod-player-skills
```

## 优势

| 优势                       | 说明                                                         |
| -------------------------- | ------------------------------------------------------------ |
| **结构化知识注入**         | 将腾讯云播放器 SDK 领域知识直接注入 Agent 上下文，降低歧义，提升输出一致性 |
| **与 Prompt 工程深度配合** | 内置标准化执行流程，帮助 Agent 按可复用路径完成播放器集成    |
| **零运行时依赖**           | Skills 为静态文本资源，无需额外启动服务                      |
| **全平台覆盖**             | 单一技能包覆盖 Android / iOS / Flutter / Web / HarmonyOS 五大平台的播放器集成 |
| **场景全面**               | 覆盖点播播放、短视频组件、视频下载、预加载、画中画、VR 播放等丰富场景 |


## 使用示例

安装 Skills 后，您可以直接向 AI Agent 描述需求，Agent 将自动调用 Skills 中的结构化知识完成任务：

### 示例 1：集成播放器

``` bash
请帮我在 Android 项目中集成腾讯云播放器 SDK，实现 MP4 视频播放功能
```

### 示例 2：实现视频下载

``` bash
我需要在 iOS 应用中实现视频离线缓存功能，支持断点续传
```

### 示例 3：短视频场景

``` bash
使用 TUIPlayerKit 实现一个类似抖音的短视频滑动播放页面
```

### 示例 4：多平台对比

``` bash
对比 Android 和 iOS 平台的播放器 API 差异，列出主要的类和方法
```

## 触发关键词

当您的提问包含以下关键词时，AI Agent 会自动激活 Player SDK Skills：

- 中文关键词：`腾讯云播放器 SDK`、`播放器`、`点播播放`、`视频播放`、`播放器集成`、`视频下载`、`预下载`、`播放器License`、`短视频组件`、`画中画`、`VR播放`、`全景视频`、`终端极速高清`、`超分`

- 英文关键词：`TXVodPlayer`、`TCPlayer`、`Player SDK`、`Vod Player`、`TXVodPlayConfig`、`TXVodDownloadManager`、`TXVodPreloadManager`、`SuperPlayerPlugin`、`TUIPlayerShortVideo`、`TUIPlayerKit`、`TUIPIP`


## Skills 内容结构

``` bash
tencent-vod-player-skills/
├── SKILL.md                              # Skills 主文件
├── LICENSE.txt                           # 开源协议
└── references/                           # 参考文档
    ├── android-integration.md            # Android 集成指南
    ├── android-api.md                    # Android API 文档
    ├── ios-integration.md                # iOS 集成指南
    ├── ios-api.md                        # iOS API 文档
    ├── flutter-integration.md            # Flutter 集成指南
    ├── flutter-api.md                    # Flutter API 文档
    ├── web-integration.md                # Web 集成指南
    ├── web-api.md                        # Web API 文档
    ├── harmonyos-integration.md          # HarmonyOS 集成指南
    ├── harmonyos-api.md                  # HarmonyOS API 文档
    ├── license-guide.md                  # License 配置指南
    ├── advanced-features.md              # 高级功能（短视频/画中画/VR/超分）
    └── faq.md                            # 常见问题
```

## 参考

- [Tencent Vod Player SDK Skills (GitHub)](https://github.com/Tencent-Cloud/tencent-vod-player-skills)

- [腾讯云播放器 SDK 官方文档](https://cloud.tencent.com/document/product/881)
