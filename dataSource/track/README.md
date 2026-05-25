```
无人机轨迹数据及解析实例
```

# 无人机轨迹数据

- 解析无人机数据需要在 [DJI开发者平台](https://developer.dji.com/cn/) 上注册账号并创建应用，获取API密钥。
- 进入个人中心 -> 创建应用 -> APP Type 选择 Open API 

## 数据解析工具

- ~~[dji-log-parser](https://github.com/lvauvillier/dji-log-parser)~~ — 高度数据有偏差，已弃用
- [FlightRecordParsingLib](https://github.com/dji-sdk/FlightRecordParsingLib) — DJI 官方解析库，推荐

## json_result.json 解析结果

`json_result.json` 是 DJI 飞行日志的解析结果（95.7万行），由 `FlightRecordParsingLib` 解析生成。

### 飞行概况

| 项目 | 数值 |
|------|------|
| 机型 | DJI MINI3 |
| App 版本 | 1.13.12 |
| 采样率 | 10 Hz |
| 总帧数 | 5,440 帧 |
| 总时间 | 544.1 s（约 9.1 分钟）|
| 总距离 | 1,221.38 m |
| 最大高度 | 318.2 m |
| 最大水平速度 | 10.75 m/s |
| 最大垂直速度 | 3.3 m/s |
| 起始时间 | 2026-05-24 11:48:03 |

### 设备序列号

- **飞控**: `687CKCQ001AK28`
- **遥控器**: `5HAZKCV0042WF6`
- **相机**: `53HFKBK0M60AGV`
- **电池**: `52XPKCHCA208A6`

### 空间范围

- **纬度**: 30.391258 ~ 30.395118
- **经度**: 104.100949 ~ 104.104361
- **高度**: 0.0 ~ 318.2 m

### JSON 结构

```json
{
  "summary": {              // 飞行摘要
    "aircraftName",         // 机型
    "startTime",            // 起始时间戳
    "startCoordinate",      // 起始坐标 (lat, lon)
    "totalDistance",        // 总飞行距离(m)
    "totalTime",            // 总飞行时间(s)
    "maxHeight",            // 最大高度(m)
    "maxHorizontalSpeed",   // 最大水平速度(m/s)
    "batteriesInformation", // 电池信息
    "camerasInformation",   // 相机信息
    "flightControllerInformation", // 飞控信息
    ...
  },
  "info": {
    "frameTimeStates": [    // 每帧状态数组（5,440个元素）
      {
        "flightControllerState": {
          "aircraftLocation": { "latitude", "longitude" },
          "altitude",         // 高度
          "velocity": { "velocityX", "velocityY", "velocityZ" },
          "attitude": { "pitch", "roll", "yaw" },
          "flightMode",       // 飞行模式
          "GPSSignalLevel",   // GPS信号等级
          "satelliteCount",   // 卫星数
          ...
        },
        "cameraState": { ... },
        "gimbalState": { ... },
        "batteryState": { ... },
        "airLinkState": { ... },
        "visionState": { ... }
      }
    ]
  }
}
```

### 飞行模式序列

`MotorsJustStarted` → `AssistedTakeoff` → `GPSAtti`（正常飞行）→ `GoHome`（返航）→ `AutoLanding`（自动降落）

