---
title: "Edge AI for Robots: Running VLA Models on Jetson, NPUs, and Mobile Hardware"
date: 2026-04-03
draft: false
tags: ["edge AI", "NVIDIA Jetson", "NPU", "deployment", "optimization", "quantization"]
categories: ["Tools & Frameworks"]
series: ["From Zero to Robot Engineer"]
weight: 5
summary: "How to deploy VLA and robot learning models on edge hardware — from NVIDIA Jetson to mobile NPUs — with practical optimization techniques for real-time robot control."
ShowToc: true
TocOpen: true
---

Your VLA model runs beautifully on an A100. Now you need it running at 30Hz on a robot with a 15W power budget. Welcome to edge AI deployment for robotics.

## The Edge Hardware Landscape in 2026

| Hardware | Power | Compute | Memory | Price | Best For |
|----------|-------|---------|--------|-------|----------|
| NVIDIA Jetson Orin NX | 15-25W | 100 TOPS | 8-16GB | $400-600 | Production humanoids |
| NVIDIA Jetson AGX Orin | 15-60W | 275 TOPS | 32-64GB | $1,000-2,000 | High-end robots |
| Qualcomm RB5 | 8-15W | 15 TOPS | 8GB | $300 | Mobile robots |
| Rockchip RK3588 | 5-10W | 6 TOPS | 8-16GB | $50-100 | Budget robots |
| Apple M-series (via Mac Mini) | 10-30W | 38 TOPS (Neural Engine) | 16-32GB | $500-800 | Research/prototyping |

**The bottleneck is usually memory bandwidth, not compute.** A Jetson Orin has 35-60 TOPS but only 68-204 GB/s memory bandwidth. VLA models are memory-bound during inference.

## Optimization Techniques

### 1. Quantization

Reduce model precision from FP32 to INT8 or even INT4.

**Impact:** 2-4x speedup, 2-4x memory reduction, minimal accuracy loss.

```python
# TensorRT INT8 quantization for Jetson
import tensorrt as trt

builder = trt.Builder(logger)
config = builder.create_builder_config()
config.set_flag(trt.BuilderFlag.INT8)

# Calibrate with representative robot data
config.int8_calibrator = RobotDataCalibrator(calibration_dataset)
```

**Rule of thumb for robotics:**
- FP16: Safe for all models, 2x speedup over FP32
- INT8: Works for perception and policy networks, needs calibration
- INT4/1-bit: Experimental, use ParetoQ or BitNet frameworks

### 2. Model Pruning

Remove unnecessary neurons/channels from the model.

**Structured pruning** (remove entire channels) is more practical for deployment than unstructured pruning, because it produces models that run faster on standard hardware without sparse matrix support.

### 3. Knowledge Distillation

Train a small "student" model to mimic a large "teacher" model.

For VLA models, this typically means:
- Teacher: 7B parameter VLA trained on diverse data
- Student: 1-2B parameter model fine-tuned on teacher's outputs + your specific task data
- Result: 80-90% of teacher performance at 3-5x faster inference

### 4. Speculative Execution for Robot Control

An emerging technique: use a small, fast model for most control steps, and a larger model for verification/correction.

```
Fast model (1B, 50Hz) → generates actions every 20ms
Slow model (7B, 5Hz) → verifies and corrects every 200ms
```

This gives you the responsiveness of the small model with the accuracy of the large model.

## Deployment Pipeline

### For NVIDIA Jetson

```bash
# 1. Export model to ONNX
python export_onnx.py --model vla_policy --output policy.onnx

# 2. Optimize with TensorRT
trtexec --onnx=policy.onnx \
        --saveEngine=policy.trt \
        --fp16 \
        --workspace=4096

# 3. Run inference
python infer_trt.py --engine policy.trt --camera /dev/video0
```

### For Qualcomm/Rockchip NPUs

These platforms typically use their own inference frameworks:
- Qualcomm: SNPE (Snapdragon Neural Processing Engine)
- Rockchip: RKNN

The workflow is similar: export to ONNX, convert to the platform's format, optimize, deploy.

## Real-World Performance Benchmarks

Typical inference latencies for a 2B VLA model on edge hardware:

| Hardware | FP16 | INT8 | Achievable Hz |
|----------|------|------|---------------|
| Jetson AGX Orin 64GB | 25ms | 15ms | 40-66 Hz |
| Jetson Orin NX 16GB | 45ms | 28ms | 22-35 Hz |
| Qualcomm RB5 | 80ms | 50ms | 12-20 Hz |
| Rockchip RK3588 | 150ms | 90ms | 7-11 Hz |

**For most manipulation tasks, 10-30 Hz is sufficient.** Bipedal locomotion typically requires 30+ Hz.

## Power-Performance Trade-offs

The Jetson platform allows dynamic power mode selection:

| Mode | Power | Performance | Use Case |
|------|-------|-------------|----------|
| 15W | Low | ~60% of max | Battery-powered mobile robot |
| 30W | Medium | ~80% of max | Plugged-in manipulation |
| 60W (AGX only) | High | 100% | Maximum performance |

For battery-powered robots, you can dynamically switch between power modes based on task demands — low power for navigation, high power for manipulation.

## Common Pitfalls

1. **Don't benchmark on desktop GPU and assume edge will be proportional.** Memory bandwidth limitations change the scaling behavior.

2. **Test with real camera input, not pre-loaded images.** Camera capture and preprocessing add 5-15ms that lab benchmarks miss.

3. **Account for thermal throttling.** Edge devices in robot enclosures run hot. Test with sustained workloads, not just burst benchmarks.

4. **Don't forget the other compute tasks.** Your robot also needs to run SLAM, path planning, and communication stacks. Budget only 50-60% of compute for the AI model.

## Further Reading

- [Getting Started with Embodied AI](/posts/getting-started-embodied-ai-2026/)
- [5 Robot Learning Frameworks](/posts/robot-learning-frameworks-2026/)
- [Sim-to-Real Transfer Guide](/posts/sim-to-real-transfer-guide/)
