---
title: "Learning Path: From Zero to Robot Engineer"
layout: "single"
url: "/learning-path/"
summary: "A structured 5-step learning path through embodied AI — from first concepts to production deployment."
ShowToc: true
TocOpen: true
---

This is your guided path through embodied AI. Each step builds on the previous one. Budget 2-4 weeks per step if you're learning part-time.

## The Path

```
Step 1          Step 2              Step 3           Step 4          Step 5
Foundations  →  Tools & Frameworks  →  Sim-to-Real  →  Real Data  →  Edge Deploy
   │                │                    │              │              │
   ▼                ▼                    ▼              ▼              ▼
 Concepts        Simulators          Bridge the      Collect &      Ship to
 & Terms         & Libraries         gap              Train          hardware
```

---

### Step 1: Foundations

**Goal:** Understand the core concepts and vocabulary of embodied AI.

**Read:** [Getting Started with Embodied AI in 2026](/posts/getting-started-embodied-ai-2026/)

**Key takeaways:**
- The Perceive → Think → Act → Observe loop
- What VLA models are and why they matter
- The difference between imitation learning and reinforcement learning
- Where simulation fits in the pipeline

**Reference:** Keep the [Embodied AI Glossary](/posts/embodied-ai-glossary/) bookmarked — you'll come back to it often.

**Time:** 1 week

---

### Step 2: Tools & Frameworks

**Goal:** Set up your development environment and run your first simulation.

**Read:** [5 Robot Learning Frameworks You Should Know](/posts/robot-learning-frameworks-2026/)

**Hands-on:**
1. Install MuJoCo and run a basic simulation
2. Try Gymnasium Robotics environments
3. If you have an NVIDIA GPU: install Isaac Lab and run a parallel training

**Key takeaways:**
- MuJoCo for fast iteration, Isaac Lab for scale
- LeRobot for real-world data, ROS 2 for deployment
- How these tools compose into a full pipeline

**Time:** 2 weeks

---

### Step 3: Sim-to-Real Transfer

**Goal:** Understand why simulation-trained policies fail in the real world, and how to fix it.

**Read:** [Sim-to-Real Transfer Guide](/posts/sim-to-real-transfer-guide/)

**Hands-on:**
1. Train a policy in MuJoCo with domain randomization
2. Compare performance with and without randomization
3. If you have real hardware: attempt a basic transfer

**Key takeaways:**
- The three gaps: visual, physics, dynamics
- Domain randomization as the brute-force solution
- System identification as the precise solution
- Real-to-sim-to-real as the modern approach

**Time:** 2 weeks

---

### Step 4: Real-World Data Collection & Training

**Goal:** Collect real robot data and train policies that work on physical hardware.

**Read:** [LeRobot Tutorial](/posts/lerobot-tutorial-getting-started/)

**Hands-on:**
1. Install LeRobot
2. Explore pre-trained policies on the Hub
3. If you have a robot (even a simple arm): collect 20+ demonstrations
4. Train an ACT or Diffusion Policy on your data

**Key takeaways:**
- Standardized data formats matter
- 10-20 real demonstrations dramatically improve transfer
- Policy architecture choice depends on your task

**Time:** 2-3 weeks

---

### Step 5: Edge Deployment

**Goal:** Deploy your trained policy on resource-constrained robot hardware.

**Read:** [Edge AI for Robots](/posts/edge-ai-robot-deployment/)

**Hands-on:**
1. Export a trained model to ONNX
2. Optimize with TensorRT (if on Jetson) or platform-specific tools
3. Benchmark inference latency and throughput
4. Run a complete perception → inference → action loop

**Key takeaways:**
- Quantization (FP16/INT8) is your best friend
- Memory bandwidth, not compute, is usually the bottleneck
- Budget only 50-60% of edge compute for the AI model
- Test with real sensors, not pre-loaded data

**Time:** 2 weeks

---

## After the Path

You now have end-to-end skills. Go deeper in the area that interests you most:

| Interest | Next Reading |
|----------|-------------|
| Model architectures | [VLA Models Compared](/posts/vla-models-compared-2026/), [Neurosymbolic VLA](/posts/neurosymbolic-vla-explained/), [Diffusion Policy](/posts/diffusion-policy-explained/) |
| Hardware platforms | [Humanoid Landscape](/posts/humanoid-robot-landscape-2026/), [Hardware Comparison](/posts/unitree-g1-vs-figure-02-vs-optimus/), [Open-Source Projects](/posts/open-source-humanoid-projects/) |
| Industry & careers | [Funding Tracker](/posts/embodied-ai-funding-tracker-2026/), [China Companies](/posts/china-humanoid-robot-companies/), [Safety Standards](/posts/robot-safety-standards-2026/) |
