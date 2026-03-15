# Repositories

[中文](repositories_CN.md) | English

This document describes the three sub-repositories and their roles.

---

## vision-follow-car-mcu

| Item | Description |
|------|-------------|
| **Role** | Low-level control firmware |
| **Platform** | Daliang Go-kart v1.0 development board (STM32F103RCT6) |
| **Main responsibilities** | Steering control, throttle control, brake control, mode switching, UART communication with Jetson |

This repository contains the STM32 firmware for the Daliang Go-kart v1.0 development board. It receives control commands from Jetson over UART, parses them, and drives the steering mechanism, throttle, and brake actuator.

**Relationship to other repos**:
- During data collection, sends vehicle data to `vision-follow-car-jetson`
- Receives commands from `vision-follow-car-jetson`
- No direct dependency on `vision-follow-car-training`

---

## vision-follow-car-training

| Item | Description |
|------|-------------|
| **Role** | Server-side training pipeline |
| **Platform** | Server (Linux/Windows, with GPU) |
| **Main responsibilities** | Data loading and preprocessing, data cleaning, model training, validation, model export |

This repository contains the offline training pipeline. It takes collected image–control pairs, trains a vision-based control model, validates it, and exports a model artifact for deployment on Jetson.

**Relationship to other repos**:
- Produces the model consumed by `vision-follow-car-jetson`
- Does not run on the vehicle; no direct interaction with `vision-follow-car-mcu`

---

## vision-follow-car-jetson

| Item | Description |
|------|-------------|
| **Role** | Onboard training data collection, inference, and control output |
| **Platform** | Jetson Orin Nano |
| **Main responsibilities** | CSI camera capture, multi-frame buffer, neural network inference, control command output to MCU |

This repository contains the onboard inference application. It acquires images from the CSI camera, maintains a frame buffer, runs the deployed model, and sends control commands to the STM32 over UART.

**Relationship to other repos**:
- Receives training data (vehicle control signals) from `vision-follow-car-mcu` during data collection
- Loads models produced by `vision-follow-car-training`
- Sends commands to `vision-follow-car-mcu`

---

## Dependency Graph

```
vision-follow-car-training
         │
         │ (exports model)
         v
vision-follow-car-jetson
         │
         │ (UART commands)
         v
vision-follow-car-mcu
```
