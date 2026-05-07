---
title: "7 Open-Source Humanoid Robot Projects You Can Build or Contribute To"
date: 2026-04-02
lastmod: 2026-05-07
draft: false
tags: ["open source", "DIY", "humanoid robot", "community", "projects"]
categories: ["Robot Hardware"]
summary: "A curated list of open-source humanoid robot projects in 2026 — from full builds you can assemble to software stacks you can contribute to."
ShowToc: true
TocOpen: true
---

<div class="tldr">
<div class="tldr-label">TL;DR · Key Takeaways</div>

- **Cheapest full humanoid is $1.5-3K** — InMoov, 3D-printable, but teleoperated rather than AI-driven
- **K-Scale Stompy ($10-15K)** — Open-source bipedal designed for VLA and RL research, with full MuJoCo simulation model
- **Koch v1.1 at ~$500** — The standard open-source arm for LeRobot data collection, buildable in a weekend
- **Software stack is converging** — LeRobot + OpenVLA + MuJoCo Menagerie + Isaac Lab now covers the full pipeline from data collection to scaled training

</div>

You don't need a $100K budget to work with humanoid robots. The open-source community has produced remarkable projects ranging from affordable full builds to sophisticated software frameworks. Here are seven worth your time.

## How This List Was Selected

This list is not a generic directory. A project had to satisfy at least two of the following criteria:

- public documentation or source code is available
- the project can be built, simulated, or extended by an outside team
- there is a real community, lab, or company maintaining it
- the project helps with a concrete robotics workflow such as manipulation data collection, simulation, or VLA evaluation

That means a fully commercial robot with no SDK is excluded, even if it is more capable. It also means some projects are included as software or research infrastructure rather than complete humanoid bodies.

## Hardware Projects

### 1. InMoov — The Original Open-Source Humanoid

**What:** Full-size humanoid robot, 3D-printable  
**Official source:** [inmoov.fr](https://inmoov.fr/)
**Cost:** ~$1,500-3,000 in parts
**License:** CC-BY-NC

The granddaddy of open-source humanoids. InMoov has been around since 2012 and has the largest community of any open-source humanoid project. Every part is 3D-printable, and the build documentation is extensive.

**Best for:** Makers and educators who want a full-size humanoid without industrial-grade requirements. The community forum has thousands of builders sharing modifications and solutions.

**Limitation:** Not designed for autonomous operation or AI-driven control. Primarily a teleoperated/scripted platform.

### 2. Stompy by K-Scale Labs

**What:** Open-source bipedal humanoid designed for AI research  
**Official source:** [K-Scale documentation](https://docs.kscale.dev/docs/getting-started)
**Cost:** ~$10,000-15,000 in parts
**License:** MIT

A serious research platform. Stompy is designed from the ground up for VLA model deployment and reinforcement learning. Includes custom actuators, integrated compute (Jetson Orin), and a full simulation model in MuJoCo.

**Best for:** Research labs wanting a lower-cost alternative to Unitree G1 with full hardware customization. The simulation model means you can start software development before building the hardware.

### 3. Koch v1.1 — Open-Source Robot Arm

**What:** Low-cost 6-DOF robot arm for manipulation research  
**Official source:** [Hugging Face LeRobot](https://github.com/huggingface/lerobot)
**Cost:** ~$500 in parts
**License:** Apache 2.0

Not a humanoid, but essential for manipulation research. Koch is the standard open-source arm used with LeRobot for data collection and policy deployment. If you're starting in robot learning, this is the most accessible hardware entry point.

**Best for:** Anyone starting in robot manipulation. Build one in a weekend, start collecting demonstration data by Monday.

## Software Projects

### 4. LeRobot (Hugging Face)

**What:** End-to-end robot learning framework
**Official source:** [github.com/huggingface/lerobot](https://github.com/huggingface/lerobot)
**License:** Apache 2.0
**GitHub Stars:** 15K+

Already covered in our [tutorial](/posts/lerobot-tutorial-getting-started/), but it deserves a spot on this list. LeRobot is the most important open-source project in robot learning right now. Data collection, policy training, model sharing — all in one framework.

**Contribute:** The project actively welcomes contributions. High-impact areas include new policy architectures, hardware driver support, and benchmark tasks.

### 5. OpenVLA

**What:** Open-source Vision-Language-Action foundation model
**Official source:** [github.com/openvla/openvla](https://github.com/openvla/openvla)
**License:** MIT
**Models available:** 7B, 3B, 1B variants

The community's answer to proprietary VLA models like GR00T and pi0. Trained on the Open X-Embodiment dataset, OpenVLA provides pre-trained weights, training code, and fine-tuning scripts.

**Contribute:** Data quality improvement (the OXE dataset has inconsistencies), new model architectures, and benchmark evaluations are all active areas.

### 6. MuJoCo Menagerie

**What:** Collection of high-quality robot simulation models
**Official source:** [google-deepmind/mujoco_menagerie](https://github.com/google-deepmind/mujoco_menagerie)
**License:** Apache 2.0

A curated set of robot models for MuJoCo, including humanoid robots (Unitree G1, H1), robot arms (UR5, Franka), and hands (Shadow, Allegro). These models are research-grade — accurate dynamics, proper collision geometry, and calibrated actuator models.

**Best for:** Anyone doing simulation-based robot learning. Instead of building your own robot model (error-prone and time-consuming), start with a validated model from the Menagerie.

### 7. Isaac Lab

**What:** GPU-accelerated robot learning framework
**Official source:** [isaac-sim.github.io/IsaacLab](https://isaac-sim.github.io/IsaacLab/)
**License:** BSD-3
**By:** NVIDIA

The open-source layer on top of Isaac Sim. Provides pre-built RL training environments, domain randomization pipelines, and integration with popular RL libraries. While Isaac Sim itself requires an NVIDIA GPU, Isaac Lab's abstractions make it the most productive environment for large-scale robot policy training.

**Contribute:** Custom environments, new task definitions, and reward function engineering. The framework is designed for extensibility.

## How to Choose

| If you want to... | Start with |
|-------------------|------------|
| Build a physical robot | InMoov (fun) or Koch (research) |
| Train robot AI policies | LeRobot + MuJoCo Menagerie |
| Work on VLA models | OpenVLA |
| Train at scale | Isaac Lab |
| Do everything | Koch + LeRobot + MuJoCo → Isaac Lab for scaling |

## Contributing to Open-Source Robotics

The embodied AI open-source community is smaller and more impactful per contributor than web/mobile open source. High-value contributions:

1. **Bug reports with reproduction steps** — Especially for sim-to-real discrepancies
2. **New simulation environments** — Task diversity is a bottleneck
3. **Documentation and tutorials** — The field grows faster when onboarding is easier
4. **Benchmark results** — Reproducible comparisons across methods
5. **Hardware driver support** — Making LeRobot work with new robot platforms

Every major robotics company monitors these open-source projects. Contributing is also one of the best ways to get hired in the field.

## What to Verify Before You Build

Before buying parts or committing lab time, check:

| Check | Why it matters |
|---|---|
| Active repository commits | Abandoned robot code becomes expensive fast |
| Bill of materials freshness | Parts disappear or change revisions |
| Simulator parity | A robot that cannot be simulated slows policy work |
| License terms | Non-commercial licenses may block startup or product use |
| Safety envelope | Open-source does not mean safe for unsupervised operation |

For most engineers, the best path is still incremental: start with a low-cost arm and LeRobot, move into MuJoCo or Isaac Lab, then decide whether a full humanoid platform is justified.

<div class="source-box">
<strong>Source notes:</strong>
This page uses official project pages and repositories where possible: InMoov, K-Scale documentation, Hugging Face LeRobot, OpenVLA, Google DeepMind MuJoCo Menagerie, and NVIDIA Isaac Lab. Cost ranges are directional because open-source robot builds vary by region, parts availability, printer quality, and optional sensors.
</div>
