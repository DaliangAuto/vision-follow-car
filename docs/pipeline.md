# End-to-End Pipeline

[中文](pipeline_CN.md) | English

This document describes the full technical workflow from data collection to in-vehicle execution.

---

## Pipeline Stages

```
Data Collection → Data Cleaning → Model Training → Model Export → Jetson Deployment → MCU Execution
```

---

## Stage 1: Data Collection

| Item | Description |
|------|-------------|
| **Input** | None (manual driving) |
| **Output** | Raw data: images and corresponding control signals |
| **Purpose** | Capture human driving behavior for imitation learning |

The operator drives the car manually. The MCU sends vehicle control data (steering, throttle, brake) to the compute module via UART. The compute module records camera images and control signals in sync. These paired samples form the basis for training.

Handled by: `vision-follow-car-jetson` (recording) or a dedicated collection setup that produces compatible data.

---

## Stage 2: Data Cleaning

| Item | Description |
|------|-------------|
| **Input** | Raw data from Stage 1 |
| **Output** | Cleaned dataset: valid image–control pairs |
| **Purpose** | Remove invalid or inconsistent samples, prepare for training |

Invalid frames (e.g., blur, missing control, misalignment) are filtered out. The remaining data is organized into a consistent format for model training.

Handled by: `vision-follow-car-training`.

---

## Stage 3: Model Training

| Item | Description |
|------|-------------|
| **Input** | Cleaned dataset |
| **Output** | Trained model weights / checkpoint |
| **Purpose** | Learn a mapping from image to control from human demonstrations |

A neural network is trained to predict control outputs from image inputs. Training runs on a server with GPU acceleration.

Handled by: `vision-follow-car-training`.

---

## Stage 4: Model Export

| Item | Description |
|------|-------------|
| **Input** | Trained model (checkpoint) |
| **Output** | Deployable model format suitable for Jetson inference |
| **Purpose** | Convert the training artifact into a format the inference engine can load |

The model is exported to a format (e.g., ONNX, TensorRT, or framework-specific) that can be loaded on Jetson for real-time inference.

Handled by: `vision-follow-car-training`.

---

## Stage 5: Jetson Deployment

| Item | Description |
|------|-------------|
| **Input** | Exported model, live camera stream |
| **Output** | Real-time control commands (steering, throttle, brake) |
| **Purpose** | Run inference on the vehicle and produce control output |

The exported model is deployed on Jetson Orin Nano. Images from the CSI camera are fed into the model, which outputs control values. These values are sent to the MCU over UART.

Handled by: `vision-follow-car-jetson`.

---

## Stage 6: MCU Execution

| Item | Description |
|------|-------------|
| **Input** | Control commands from Jetson (UART) |
| **Output** | Physical actuation: steering angle, throttle, brake state |
| **Purpose** | Translate commands into hardware actions |

The STM32 receives the commands, converts them into PWM or other control signals, and drives the steering mechanism, throttle, and brake actuator.

Handled by: `vision-follow-car-mcu`.

---

## Summary Table

| Stage | Input | Output | Project |
|-------|-------|--------|---------|
| Data Collection | Manual driving | Raw images + controls | Jetson / collection setup |
| Data Cleaning | Raw data | Clean dataset | vision-follow-car-training |
| Model Training | Clean dataset | Trained model | vision-follow-car-training |
| Model Export | Trained model | Deployable model | vision-follow-car-training |
| Jetson Deployment | Model + camera | Control commands | vision-follow-car-jetson |
| MCU Execution | Control commands | Actuator signals | vision-follow-car-mcu |
