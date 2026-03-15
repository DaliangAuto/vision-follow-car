# vision-follow-car

[English](README.md) | 中文

基于单目视觉的自动跟随小车系统。涵盖数据采集、模型训练、推理部署与实车控制的完整链路。

---

## 项目概述

本项目实现了一套完整的视觉跟随小车系统：以单目相机为感知输入，Jetson Orin Nano 负责实时推理，STM32F103RCT6 负责底层执行控制。系统由三个在不同硬件上运行的子项目组成：

- **训练端** — 服务器端数据处理与模型训练
- **推理端** — Jetson Orin Nano 上的图像采集与神经网络推理
- **控制端** — STM32 固件负责转向、油门、刹车与串口通信

---

## 系统架构

```
                    +------------------+
                    |   CSI 摄像头     |
                    |   (IMX219)       |
                    +--------+---------+
                             |
                             v
+------------------+   +-----+------+   +------------------+
| vision-follow-   |   |  Jetson    |   | vision-follow-   |
| car-training     |   | Orin Nano  |   | car-mcu          |
| (服务器)          |   |            |   | (STM32)          |
|                  |   | 推理与输出   |   |                  |
| 数据/训练/导出    |-->| 控制指令    |-->| 转向/油门/刹车    |
+------------------+   +------------+   +------------------+
```

数据流：图像 → Jetson（推理）→ 控制指令 → MCU（执行）。

---

## 子仓库 / 子项目

| 仓库 | 角色 | 平台 |
|------|------|------|
| [vision-follow-car-mcu](https://github.com/YOUR_ORG/vision-follow-car-mcu) | 底层控制（转向、油门、刹车、串口协议） | Daliang Go-kart v1.0 |
| [vision-follow-car-training](https://github.com/YOUR_ORG/vision-follow-car-training) | 数据处理、模型训练、验证、导出 | 服务器 |
| [vision-follow-car-jetson](https://github.com/YOUR_ORG/vision-follow-car-jetson) | 摄像头采集、多帧缓存、推理、输出控制指令 | Jetson Orin Nano |

---

## 硬件平台

- **主控 / 算力**: Jetson Orin Nano
- **MCU 控制板**: Daliang Go-kart v1.0
- **摄像头**: CSI 摄像头（IMX219）
- **执行机构**: 转向机构、刹车电缸

详见 [docs/hardware.md](docs/hardware.md)。

---

## 端到端流程

1. **数据采集** — 人工驾驶，录制图像与控制信号
2. **数据清洗** — 剔除无效样本，形成图像–控制配对的训练数据
3. **模型训练** — 在服务器上训练视觉跟随模型
4. **模型导出** — 导出可用于推理的模型
5. **Jetson 部署** — 在 Jetson 上加载模型，实时推理
6. **MCU 执行** — Jetson 将控制指令下发给 STM32 进行执行

详见 [docs/pipeline.md](docs/pipeline.md)。

---

## Demo

*演示视频与截图将在此处更新。*

---

## 文档

| 文档 | English | 中文 |
|------|---------|------|
| 系统总体概述 | [docs/system_overview.md](docs/system_overview.md) | [docs/system_overview_CN.md](docs/system_overview_CN.md) |
| 硬件说明 | [docs/hardware.md](docs/hardware.md) | [docs/hardware_CN.md](docs/hardware_CN.md) |
| 技术流程 | [docs/pipeline.md](docs/pipeline.md) | [docs/pipeline_CN.md](docs/pipeline_CN.md) |
| 子仓库说明 | [docs/repositories.md](docs/repositories.md) | [docs/repositories_CN.md](docs/repositories_CN.md) |
| 数据集 | [docs/dataset.md](docs/dataset.md) | [docs/dataset_CN.md](docs/dataset_CN.md) |

---

## License

MIT License
