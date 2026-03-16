# vision-follow-car

[中文](README_CN.md) | English

This project serves as a hands-on learning project for AI, covering the full pipeline from data collection and model training to deployment on embedded hardware.

A monocular vision-based auto-follow toy car system. End-to-end pipeline from data collection and model training to onboard inference and MCU control.

---

## Project Overview

This project implements a complete vision-follow vehicle system using a single camera for perception, a Jetson Orin Nano for real-time inference, and an STM32F103RCT6 MCU for low-level control. The system consists of three subprojects that run on different hardware:

- **Training** — Server-side data processing and model training
- **Inference** — Jetson Orin Nano for camera capture and neural network inference
- **Control** — STM32 firmware for steering, throttle, brake, and serial communication

---

## System Architecture

```
                    +------------------+
                    |   CSI Camera     |
                    |   (IMX219)       |
                    +--------+---------+
                             |
                             v
+------------------+   +-----+------+   +------------------+
| vision-follow-   |   |  Jetson    |   | vision-follow-   |
| car-training     |   | Orin Nano  |   | car-mcu          |
| (Server)         |   |            |   | (STM32)          |
|                  |   | Inference  |   |                  |
| Data / Train /   |-->| & Control  |-->| Steering /       |
| Export           |   | Output     |   | Throttle / Brake |
+------------------+   +------------+   +------------------+
```

Data flow: Images → Jetson (inference) → Control commands → MCU (execution).

---

## Repositories / Subprojects

| Repository | Role | Platform |
|------------|------|----------|
| [vision-follow-car-mcu](https://github.com/DaliangAuto/vision-follow-car-mcu) | Low-level control (steering, throttle, brake, serial protocol) | Daliang Go-kart v1.0 |
| [vision-follow-car-training](https://github.com/DaliangAuto/vision-follow-car-training) | Data processing, model training, validation, export | Server |
| [vision-follow-car-jetson](https://github.com/DaliangAuto/vision-follow-car-jetson) | Camera capture, multi-frame buffer, inference, control output | Jetson Orin Nano |

---

## Hardware Platform

- **Compute**: Jetson Orin Nano
- **MCU board**: Daliang Go-kart v1.0
- **Camera**: CSI camera (IMX219)
- **Actuators**: Steering mechanism, brake actuator

See [docs/hardware.md](docs/hardware.md) for details.

---

## End-to-End Pipeline

1. **Data collection** — Human-driven driving, images + control signals recorded
2. **Data cleaning** — Invalid samples removed, paired image–control data prepared
3. **Model training** — Vision model trained on server
4. **Model export** — Model exported for inference
5. **Jetson deployment** — Model loaded on Jetson for real-time inference
6. **MCU execution** — Jetson sends control commands to STM32 for actuation

See [docs/pipeline.md](docs/pipeline.md) for the full workflow.

---

## Demo

*Demo video and screenshots will be added here.*

---

## Documentation

| Document | English | 中文 |
|----------|---------|------|
| System Overview | [docs/system_overview.md](docs/system_overview.md) | [docs/system_overview_CN.md](docs/system_overview_CN.md) |
| Hardware | [docs/hardware.md](docs/hardware.md) | [docs/hardware_CN.md](docs/hardware_CN.md) |
| Pipeline | [docs/pipeline.md](docs/pipeline.md) | [docs/pipeline_CN.md](docs/pipeline_CN.md) |
| Repositories | [docs/repositories.md](docs/repositories.md) | [docs/repositories_CN.md](docs/repositories_CN.md) |
| Dataset | [docs/dataset.md](docs/dataset.md) | [docs/dataset_CN.md](docs/dataset_CN.md) |

---

## License

MIT License
