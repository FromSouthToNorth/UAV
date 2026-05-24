# UAV 无人机数据采集项目

本项目用于存储和管理 DJI 无人机采集的飞行轨迹数据与航拍照片，支持后续的点云生成、飞行路径分析等处理工作。

---

## 项目结构

```
UAV/
├── README.md                          # 项目说明文档
├── dataSource/
│   ├── track/                         # 飞行轨迹数据
│   │   ├── README.md                  # 轨迹数据说明
│   │   └── txt/
│   │       ├── DJIFlightRecord_2026-05-02_[13-26-51].txt   # 飞行记录 1
│   │       ├── DJIFlightRecord_2026-05-02_[15-50-21].txt   # 飞行记录 2
│   │       └── DJIFlightRecord_2026-05-24_[11-48-03].txt   # 飞行记录 3
│   └── aerial-pictures/               # 航拍照片数据
│       ├── README.md                  # 照片数据说明
│       └── img/
│           └── DJI_*.JPG              # 588 张航拍照片
```

---

## 数据集详情

### 1. 飞行轨迹数据

| 文件 | 日期 | 大小 | 说明 |
|------|------|------|------|
| DJIFlightRecord_2026-05-02_[13-26-51].txt | 2026-05-02 | 3.3 MB | 下午飞行记录 |
| DJIFlightRecord_2026-05-02_[15-50-21].txt | 2026-05-02 | 3.3 MB | 傍晚飞行记录 |
| DJIFlightRecord_2026-05-24_[11-48-03].txt | 2026-05-24 | 1.5 MB | 上午飞行记录 |

- **格式**: DJI 专有二进制日志格式（.txt 扩展名）
- **解析方式**: 需使用 DJI 官方工具或第三方解析器
- **推荐工具**: [dji-log-parser](https://github.com/lvauvillier/dji-log-parser)
- **API 要求**: 需在 [DJI 开发者平台](https://developer.dji.com/cn/) 注册并获取 API 密钥

### 2. 航拍照片

| 项目 | 数值 |
|------|------|
| 照片总数 | 588 张 |
| 格式 | JPG |
| 命名规则 | DJI_XXXX.JPG（0001 - 0999） |
| 地理信息 | 包含 EXIF GPS 经纬度坐标 |
| 用途 | 点云生成、三维重建、正射影像 |

**编号分布**:
- 存在照片: 588 张
- 缺失编号: 411 个（主要为 572-974 区间，可能为不同批次拍摄或已筛选）

---

## 快速开始

### 解析飞行日志

```bash
# 使用 dji-log-parser 解析二进制日志
# 安装: npm install -g dji-log-parser
dji-log-parser DJIFlightRecord_2026-05-24_\[11-48-03\].txt
```

### 提取照片 GPS 信息

```bash
# 使用 exiftool 批量提取
exiftool -csv -GPSLatitude -GPSLongitude -GPSAltitude dataSource/aerial-pictures/img/ > gps_data.csv
```

### 生成点云数据

推荐使用 [WebODM](https://webodm.org/download/) 处理航拍照片生成三维点云和正射影像：

```bash
# Docker 方式运行 WebODM
./webodm.sh start
# 上传 img/ 目录下的照片进行处理
```

---

## 工具链

| 用途 | 工具 | 链接 |
|------|------|------|
| 日志解析 | dji-log-parser | https://github.com/lvauvillier/dji-log-parser |
| 点云生成 | WebODM | https://webodm.org/download/ |
| GPS 提取 | exiftool | https://exiftool.org/ |
| DJI API | DJI 开发者平台 | https://developer.dji.com/cn/ |

---

## 数据流

```
无人机采集
    ├── 飞行日志 (.txt) ──> dji-log-parser ──> 轨迹数据 (CSV/JSON)
    │                                          └──> 飞行路径可视化
    │
    └── 航拍照片 (.JPG) ──> WebODM ──> 点云 / 正射影像 / DSM / DTM
                                       └──> GIS 分析
```

---

## 注意事项

1. **日志文件为二进制格式**，直接用文本编辑器打开会显示乱码，需使用专用解析工具
2. **照片已包含 GPS 坐标**，可直接用于地理配准和三维重建
3. **WebODM 处理耗时较长**，588 张照片建议在高性能机器上运行
4. **DJI API 密钥** 需在 DJI 开发者平台创建 Open API 类型应用获取

---
