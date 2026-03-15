# 子仓库说明

[English](repositories.md) | 中文

本文档介绍三个子仓库及其职责。

---

## vision-follow-car-mcu

| 项目 | 说明 |
|------|------|
| **角色** | 底层控制固件 |
| **平台** | STM32F103RCT6 |
| **主要职责** | 转向控制、油门控制、刹车控制、模式切换、与 Jetson 的 UART 通信 |

本仓库包含 STM32 固件，该固件适用于Daliang开发的Go-kart v1.0开发板。通过 UART 接收 Jetson 的控制指令，解析后驱动转向机构、油门与刹车电缸。

**与其他仓库的关系**：
- 采集训练数据时，发送车辆数据给 `vision-follow-car-jetson` 
- 接收来自 `vision-follow-car-jetson` 的指令
- 与 `vision-follow-car-training` 无直接依赖

---

## vision-follow-car-training

| 项目 | 说明 |
|------|------|
| **角色** | 服务器端训练流程 |
| **平台** | 服务器（Linux/Windows，带 GPU） |
| **主要职责** | 数据加载与预处理、数据清洗、模型训练、验证、模型导出 |

本仓库包含离线训练流程。以采集的图像–控制配对为输入，训练视觉控制模型，验证后导出供 Jetson 部署的模型产物。

**与其他仓库的关系**：
- 产出被 `vision-follow-car-jetson` 消费的模型
- 不在车端运行；与 `vision-follow-car-mcu` 无直接交互

---

## vision-follow-car-jetson

| 项目 | 说明 |
|------|------|
| **角色** | 车端训练数据采集、推理与控制输出 |
| **平台** | Jetson Orin Nano |
| **主要职责** | CSI 摄像头采集、多帧缓存、神经网络推理、向 MCU 输出控制指令 |

本仓库包含车端推理应用。从 CSI 摄像头获取图像，维护帧缓冲，运行部署的模型，通过 UART 将控制指令发送给 STM32。

**与其他仓库的关系**：
- 接收来自`vision-follow-car-mcu` 的训练数据
- 加载 `vision-follow-car-training` 产出的模型
- 向 `vision-follow-car-mcu` 下发指令

---

## 依赖关系

```
vision-follow-car-training
         │
         │ (导出模型)
         v
vision-follow-car-jetson
         │
         │ (UART 指令)
         v
vision-follow-car-mcu
```
