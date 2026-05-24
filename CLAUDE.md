# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目性质

这是一个**数据存储仓库**，用于存储 DJI 无人机采集的飞行轨迹数据，航拍照片已移至网盘，不包含代码构建系统（无 package.json、无构建脚本、无测试框架）。

## 数据结构

```
dataSource/
├── track/                          # 飞行轨迹数据
│   ├── txt/                        # DJI 二进制飞行日志（.txt 扩展名）
│   └── json/                       # 解析后的轨迹 JSON 数据
│       └── track.json              # 包含 frames 数组，每个 frame 有 osd.latitude / osd.longitude / osd.height 等字段
└── aerial-pictures/                # 航拍照片数据
    └── README.md                   # 照片说明，含夸克网盘下载链接
```

## 常用数据操作命令

### 解析飞行日志

```bash
# 使用 dji-log-parser 解析二进制日志（需先 npm install -g dji-log-parser）
dji-log-parser dataSource/track/txt/DJIFlightRecord_2026-05-24_\[11-48-03\].txt
```

### 提取照片 GPS 信息

```bash
# 照片需先从夸克网盘下载到本地
# 使用 exiftool 批量提取 GPS 数据
cd /path/to/downloaded/photos/
exiftool -csv -GPSLatitude -GPSLongitude -GPSAltitude -n *.JPG > gps_data.csv
```

### 解析轨迹 JSON

解析后的 `track.json` 结构：
- `details`: 飞行摘要（totalTime、totalDistance、maxHeight、aircraftName 等）
- `frames[]`: 帧数组，每帧包含：
  - `osd.latitude` / `osd.longitude`: GPS 坐标
  - `osd.height`: 飞行高度
  - `osd.xSpeed` / `osd.ySpeed` / `osd.zSpeed`: 速度分量
  - `osd.pitch` / `osd.roll` / `osd.yaw`: 姿态角
  - `osd.flycState`: 飞行状态

## 工具链

| 工具 | 用途 |
|------|------|
| dji-log-parser | 解析 DJI 二进制飞行日志 |
| exiftool | 提取照片 EXIF / GPS 信息 |
| WebODM | 处理航拍照片生成点云 / 正射影像 |

## DJI API 密钥获取

在 [DJI 开发者平台](https://developer.dji.com/cn/) 注册账号，创建应用（APP Type 选择 Open API）获取 API 密钥。
