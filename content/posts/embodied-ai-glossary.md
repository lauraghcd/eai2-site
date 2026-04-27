---
title: "Embodied AI Glossary: 50 Terms You Need to Know"
date: 2026-04-06
draft: false
tags: ["glossary", "beginner", "reference", "terminology"]
categories: ["Fundamentals"]
summary: "A plain-English glossary of the most important terms in embodied AI, robotics, and robot learning — from VLA to sim-to-real to PDDL."
ShowToc: true
TocOpen: true
---

<div class="tldr">
<div class="tldr-label">TL;DR · Key Takeaways</div>

- **50 essential terms** — Covers everything from action spaces and bipedal locomotion to VLA models and zero-shot transfer
- **Plain-English definitions** — Each entry written for engineers entering the field, not researchers writing papers
- **Reference, not tutorial** — Skim to fill gaps, or read end-to-end as a 10-minute primer
- **Cross-linked to deeper guides** — Key terms connect to full articles on VLA, sim-to-real, and getting started

</div>

New to embodied AI? This glossary covers the 50 most important terms you'll encounter, explained without jargon. If you want a structured learning path instead of a reference, start with our [getting started guide](/posts/getting-started-embodied-ai-2026/).

## A

**Action Space** — The set of all possible actions a robot can take. For a robot arm, this might be 6 joint angles. For a mobile robot, it might be forward speed and turning rate.

**ACT (Action Chunking with Transformers)** — A popular imitation learning algorithm that predicts a chunk of future actions at once instead of one action at a time. This makes policies smoother and more robust.

## B

**Behavior Cloning** — The simplest form of imitation learning. Train a neural network to map from observations to actions using demonstration data. Simple but can fail when the robot encounters states not seen in demonstrations.

**Bipedal Locomotion** — Walking on two legs. The defining challenge of humanoid robots. Much harder than wheeled or quadruped locomotion due to the inherent instability.

## C

**CALVIN** — A popular benchmark for language-conditioned robot manipulation. Tests a robot's ability to follow natural language instructions like "pick up the red block and put it in the drawer."

**Contact-Rich Manipulation** — Tasks where the robot makes complex physical contact with objects — assembly, tool use, insertion. These are the hardest tasks in manipulation because the physics are hard to simulate.

**Cobot (Collaborative Robot)** — A robot designed to work alongside humans safely. Unlike traditional industrial robots behind cages, cobots have force-limiting and collision detection.

## D

**Dexterous Manipulation** — Using multi-fingered robot hands to manipulate objects with human-like skill. Requires coordinating many degrees of freedom simultaneously.

**Diffusion Policy** — A policy architecture that uses diffusion models (the same technology behind image generation) to generate robot actions. Produces smooth, multi-modal action distributions.

**DOF (Degrees of Freedom)** — The number of independent ways a robot can move. A typical robot arm has 6 DOF. A humanoid robot has 20-40+ DOF.

**Domain Randomization** — Training a policy on many randomized simulation environments so it generalizes to the real world. Vary textures, lighting, physics parameters, etc.

## E

**Embodied AI** — AI that interacts with the physical world through a body (robot, drone, autonomous vehicle). Distinct from disembodied AI (chatbots, image generators) that only processes information.

**End-Effector** — The tool at the end of a robot arm — a gripper, suction cup, welding torch, etc. End-effector control means controlling the position/orientation of this tool rather than individual joint angles.

## F

**FAST (Frequency-domain Action Sequence Tokenization)** — A method for converting continuous robot actions into discrete tokens using frequency-domain analysis (DCT). Enables VLA models to use the same token-prediction approach as language models.

**Force/Torque Sensor** — A sensor that measures forces and torques at a robot's joints or end-effector. Critical for contact-rich manipulation and safe human-robot interaction.

## G

**Generalization** — A robot's ability to handle situations it wasn't explicitly trained on. The holy grail of robot learning — and the biggest gap between simulation results and real-world deployment.

**GR00T** — NVIDIA's Vision-Language-Action foundation model for humanoid robots. Part of the Isaac robotics platform.

**Grasping** — The fundamental robot manipulation skill — picking up objects. Seems simple, but robust grasping of diverse objects in cluttered environments remains an active research area.

## H

**Humanoid Robot** — A robot with a human-like body plan — bipedal legs, two arms, a torso, and a head. The form factor is controversial — some argue it's essential for human environments, others say it's unnecessarily complex.

## I

**Imitation Learning** — Training a robot by showing it what to do (demonstrations), rather than specifying a reward function. More practical than RL for many real-world tasks.

**Isaac Sim** — NVIDIA's GPU-accelerated robot simulation platform. The industry standard for large-scale robot training in simulation.

## J

**Joint Space** — The space of all possible joint angles of a robot. Planning in joint space means computing trajectories for each individual joint.

## K

**Kinematics** — The study of robot motion without considering forces. Forward kinematics: given joint angles, where is the end-effector? Inverse kinematics: given a desired end-effector position, what joint angles achieve it?

## L

**LeRobot** — Hugging Face's open-source framework for robot learning. Standardizes data collection, policy training, and model sharing.

**LIBERO** — A benchmark for lifelong robot learning. Tests a robot's ability to learn new tasks without forgetting old ones.

## M

**Manipulation** — Using robot arms and hands to interact with objects — grasping, placing, assembly, tool use. The core skill that makes robots useful.

**MuJoCo** — A fast physics simulator widely used in robot learning research. Originally from DeepMind, now open-source.

## N

**Neurosymbolic** — Combining neural networks with symbolic reasoning. In robotics, this typically means using a symbolic planner for high-level decisions and neural networks for perception and low-level control.

## O

**OXE (Open X-Embodiment)** — A large-scale dataset of robot demonstrations across many different robot types. Used to train general-purpose VLA models.

## P

**PDDL (Planning Domain Definition Language)** — A formal language for describing planning problems. Used in neurosymbolic robotics to specify task-level plans that are then executed by neural controllers.

**Policy** — A mapping from observations to actions. This is what the robot "runs" — it takes in what the robot sees and outputs what the robot should do.

**PPO (Proximal Policy Optimization)** — A popular reinforcement learning algorithm. Widely used for training robot locomotion and manipulation policies.

## R

**Reinforcement Learning (RL)** — Learning through trial and error with a reward signal. The robot tries actions, receives rewards or penalties, and gradually learns to maximize reward.

**ROS 2 (Robot Operating System 2)** — The standard middleware for robot software. Handles communication between sensors, actuators, and algorithms.

## S

**Sim-to-Real Transfer** — Deploying a policy trained in simulation on a real robot. The gap between simulation and reality is the central challenge — see our [sim-to-real transfer guide](/posts/sim-to-real-transfer-guide/) for techniques to close it.

**State Estimation** — Figuring out the current state of the robot and its environment from sensor data. Includes localization, object pose estimation, and contact state detection.

## T

**Teleoperation** — A human directly controlling a robot, usually for data collection. Common methods include VR controllers, leader-follower arms, and keyboard/gamepad.

**Tactile Sensing** — Sensors that measure physical contact — pressure, texture, slip detection. Essential for dexterous manipulation.

## V

**VLA (Vision-Language-Action)** — A type of foundation model that takes visual input and language instructions and outputs robot actions. The "foundation model for robots." See our [VLA models compared](/posts/vla-models-compared-2026/) for a head-to-head of the leading models.

## W

**Waypoint** — A target position that a robot should move through. Trajectories are often specified as sequences of waypoints.

## Z

**Zero-Shot Transfer** — Deploying a pre-trained model on a new task or environment without any additional training. The ultimate test of generalization.

---

*Missing a term? Let us know and we'll add it.*
