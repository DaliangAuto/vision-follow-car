# System Overview

[中文](system_overview_CN.md) | English

This document describes the overall architecture of the vision-follow-car project.

---

## Purpose of the Master Repository

The `vision-follow-car` repository is a **project documentation and navigation hub**. It does not contain executable code. Its role is:

- Project homepage and entry point
- High-level system description and architecture
- Links to the three sub-repositories
- Documentation for the full pipeline from data to deployment

---

## Three-Component Architecture

The system is split into three independent subprojects, each running on different hardware:

| Component | Repository | Device | Main Responsibility |
|-----------|------------|--------|---------------------|
| Training  | vision-follow-car-training | Server (CPU/GPU) | Data processing, model training, validation, export |
| Inference | vision-follow-car-jetson   | Jetson Orin Nano | Camera capture, multi-frame buffer, NN inference, control output |
| Control   | vision-follow-car-mcu      | STM32F103RCT6   | Steering, throttle, brake, mode switch, serial communication |

---

## Why Three Sub-Repositories

1. **Different development environments** — Server (Linux/Windows), embedded Linux (Jetson), and bare-metal MCU (STM32) have different toolchains, build systems, and dependencies.

2. **Different deployment targets** — Each subproject is deployed separately to its own platform. Keeping them separate avoids mixing unrelated code and configurations.

3. **Clear boundaries** — Each repository has a well-defined scope. Training produces models; Jetson consumes models and produces commands; MCU consumes commands and drives actuators.

4. **Independent versioning** — Subprojects can evolve at different paces without forcing synchronized releases.

---

## Relationship Between Components

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          Offline (Training)                                  │
│  vision-follow-car-training                                                 │
│  Raw data → Clean data → Train model → Export model                         │
└─────────────────────────────┬───────────────────────────────────────────────┘
                              │
                              │ Exported model
                              v
┌─────────────────────────────────────────────────────────────────────────────┐
│                          Online (Inference)                                  │
│  vision-follow-car-jetson                                                    │
│  CSI Camera → Image buffer → NN inference → Control commands                │
└─────────────────────────────┬───────────────────────────────────────────────┘
                              │
                              │ Serial (UART)
                              v
┌─────────────────────────────────────────────────────────────────────────────┐
│                          Execution (Control)                                 │
│  vision-follow-car-mcu                                                       │
│  Receive commands → Steering / Throttle / Brake                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

- **Training → Jetson**: The training project exports a model that the Jetson project loads for inference.

- **Jetson → MCU**: Jetson sends control commands over UART. The MCU receives them and drives the steering mechanism, throttle, and brake actuator.

- **MCU → Jetson (during data collection)**: When collecting training data, the MCU sends vehicle control data (steering, throttle, brake) to the compute module via UART. Jetson records these values in sync with camera frames for use as training labels.

---

## Data Flow Summary

1. **Perception**: CSI camera (IMX219) captures images on Jetson.
2. **Inference**: Jetson runs the neural network on image frames and outputs steering/throttle/brake values.
3. **Execution**: STM32 receives the values via serial and controls the actuators.

During **data collection**, the MCU sends control data to Jetson over UART; Jetson saves it together with video frames for training. The training pipeline operates offline and produces the model used in step 2.
