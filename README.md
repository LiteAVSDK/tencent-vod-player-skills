English| [简体中文](./README-CN.md)

# Tencent Vod Player SDK Skills User Guide

Tencent Vod Player SDK Skills provides structured guidance for Tencent Cloud VOD (Video On Demand) playback scenarios. It transforms natural language requirements into executable steps, helping AI Agents efficiently complete Player SDK integration across Android / iOS / Flutter / Web / HarmonyOS platforms.

## Prerequisites

Before using Skills, ensure your AI coding tool supports the Skills feature. Currently supported tools include:

- CodeBuddy

- Trae

- Cursor

- Codex

- Claude Code

- Claude Desktop


## Installation

### Method 1: Manual Download and Import

[Click here](https://mediacloud-76607.gzc.vod.tencent-cloud.com/aicoding/skills/tencent-vod-player-skills.zip) to download the Tencent Vod Player SDK Skills installation zip package, extract it and import into your AI coding tool's Skills directory.

### Method 2: Clone via GitHub

Clone via GitHub:

``` bash
git clone https://github.com/LiteAVSDK/tencent-vod-player-skills.git ~/.skills/tencent-vod-player-skills
```

## Advantages

| Advantage                        | Description                                                                                                    |
| -------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Structured Knowledge Injection** | Injects Tencent Cloud Player SDK domain knowledge directly into Agent context, reducing ambiguity and improving output consistency |
| **Deep Integration with Prompt Engineering** | Built-in standardized execution workflows help Agents complete player integration following reusable patterns |
| **Zero Runtime Dependencies**    | Skills are static text resources requiring no additional services to run                                       |
| **Full Platform Coverage**       | A single skill package covers player integration across Android / iOS / Flutter / Web / HarmonyOS platforms    |
| **Comprehensive Scenarios**      | Covers VOD playback, short video components, video download, preloading, picture-in-picture, VR playback and more |


## Usage Examples

After installing Skills, you can directly describe your requirements to the AI Agent, which will automatically invoke the structured knowledge in Skills to complete tasks:

### Example 1: Player Integration

``` bash
Please help me integrate Tencent Cloud Player SDK in my Android project to implement MP4 video playback
```

### Example 2: Video Download Implementation

``` bash
I need to implement offline video caching in my iOS app with resume support
```

### Example 3: Short Video Scenario

``` bash
Use TUIPlayerKit to implement a TikTok-like short video swipe playback page
```

### Example 4: Cross-Platform Comparison

``` bash
Compare the Player API differences between Android and iOS platforms, listing the main classes and methods
```

## Trigger Keywords

When your query contains the following keywords, the AI Agent will automatically activate Player SDK Skills:

- Chinese keywords: `腾讯云播放器 SDK`, `播放器`, `点播播放`, `视频播放`, `播放器集成`, `视频下载`, `预下载`, `播放器License`, `短视频组件`, `画中画`, `VR播放`, `全景视频`, `终端极速高清`, `超分`

- English keywords: `TXVodPlayer`, `TCPlayer`, `Player SDK`, `Vod Player`, `TXVodPlayConfig`, `TXVodDownloadManager`, `TXVodPreloadManager`, `SuperPlayerPlugin`, `TUIPlayerShortVideo`, `TUIPlayerKit`, `TUIPIP`


## Skills Content Structure

``` bash
tencent-vod-player-skills/
├── SKILL.md                              # Skills main file
├── LICENSE.txt                           # Open source license
└── references/                           # Reference documentation
    ├── android-integration.md            # Android Integration Guide
    ├── android-api.md                    # Android API Documentation
    ├── ios-integration.md                # iOS Integration Guide
    ├── ios-api.md                        # iOS API Documentation
    ├── flutter-integration.md            # Flutter Integration Guide
    ├── flutter-api.md                    # Flutter API Documentation
    ├── web-integration.md                # Web Integration Guide
    ├── web-api.md                        # Web API Documentation
    ├── harmonyos-integration.md          # HarmonyOS Integration Guide
    ├── harmonyos-api.md                  # HarmonyOS API Documentation
    ├── license-guide.md                  # License Configuration Guide
    ├── advanced-features.md              # Advanced Features (Short Video/PiP/VR/Super Resolution)
    └── faq.md                            # Frequently Asked Questions
```

## References

- [Tencent Vod Player SDK Skills (GitHub)](https://github.com/Tencent-Cloud/tencent-vod-player-skills)

- [Tencent Cloud Player SDK Official Documentation](https://www.tencentcloud.com/document/product/266/7836?lang=en&pg=)
