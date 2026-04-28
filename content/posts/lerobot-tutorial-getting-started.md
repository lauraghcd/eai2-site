---
title: "LeRobot Tutorial: Collect Data and Train Your First Robot Policy"
date: 2026-04-07
lastmod: 2026-04-28
draft: false
tags: ["LeRobot", "tutorial", "imitation learning", "Hugging Face", "hands-on"]
categories: ["Tools & Frameworks"]
series: ["From Zero to Robot Engineer"]
weight: 4
summary: "A step-by-step tutorial for using Hugging Face LeRobot to collect demonstration data, train an imitation learning policy, and deploy it on a simulated robot."
ShowToc: true
TocOpen: true
---

<div class="tldr">
<div class="tldr-label">TL;DR · Key Takeaways</div>

- **One framework, full pipeline** — LeRobot standardizes data collection, training, and deployment, with pre-trained policies on the Hugging Face Hub
- **ACT is the recommended starting policy** — Action Chunking with Transformers, well-supported in LeRobot, works on the ALOHA simulation benchmark out of the box
- **`pip install lerobot` is the only setup step** — Then 7 steps from a pre-trained download to a fine-tuned, evaluated, and shared policy
- **50 teleoperated demos are typically enough** — For most manipulation tasks, especially when starting from a pre-trained checkpoint
- **The LeRobot dataset format is becoming a de-facto standard** — videos for observations, parquet for low-dimensional signals, episode-aware indexing

</div>

LeRobot is Hugging Face's open-source framework for robot learning. It standardizes the entire pipeline from data collection to deployment. This tutorial walks you through the complete workflow — installation, exploring pre-trained policies, running simulation, collecting demonstrations, training, evaluating, and sharing your model — in a single sitting.

## Why LeRobot (vs Rolling Your Own)

Before LeRobot, every robot-learning project re-implemented the same scaffolding: a custom dataset loader, an ad-hoc training loop, a fragile evaluation harness, and an opaque way to share weights. The result was that papers were hard to reproduce and policies were hard to compose.

LeRobot fixes that with three opinionated decisions:

| Concern | LeRobot's choice | Alternative |
|---------|-------------------|-------------|
| Dataset format | `LeRobotDataset` (parquet + video) | Per-paper TFDS / WebDataset / RLDS |
| Policy interface | `Policy.select_action(obs)` | Each repo defines its own |
| Distribution | Hugging Face Hub | GitHub releases / Google Drive |

This means you can swap policies and datasets at the level of a string identifier, the same way you'd swap a pre-trained language model. If you're new to the broader landscape, our [Robot Learning Frameworks 2026](/posts/robot-learning-frameworks-2026/) guide compares LeRobot against Isaac Lab, RoboSuite, and MuJoCo Playground.

## Prerequisites

- **Python 3.10+** (3.11 recommended for faster decoding)
- **8 GB RAM minimum**, 16 GB recommended for ACT training
- **GPU**: optional for inference, strongly recommended for training (≥8 GB VRAM for ACT, ≥16 GB for diffusion policies)
- **Basic familiarity with PyTorch** — `nn.Module`, `DataLoader`, `optimizer.step()`
- **Disk**: ~5–20 GB per episode-rich dataset; videos dominate

If you don't have a GPU locally, Hugging Face Spaces, Modal, RunPod, and Lambda all offer hourly A10/A100 instances; the LeRobot training scripts accept a CUDA device flag and run unchanged.

## Step 1: Installation

```bash
pip install lerobot

# Verify installation
python -c "import lerobot; print(lerobot.__version__)"
```

For development against the latest commits (recommended if you're following along with active research):

```bash
pip install "lerobot[all] @ git+https://github.com/huggingface/lerobot.git"
```

The `[all]` extra pulls simulation envs (gym-aloha, gym-pusht), video codecs, and visualization tools.

## Step 2: Explore Pre-trained Policies

LeRobot hosts pre-trained policies on the Hugging Face Hub:

```python
from lerobot.common.policies.act.modeling_act import ACTPolicy
from huggingface_hub import snapshot_download

# Download a pre-trained ACT policy for ALOHA simulation
policy_path = snapshot_download("lerobot/act_aloha_sim_transfer_cube_human")
```

These policies are trained on standard benchmarks and serve as baselines or starting points for fine-tuning. Browse the full set at `huggingface.co/lerobot` — a 1-line `from_pretrained()` is usually enough to load any of them. Treat them as you would a pre-trained ResNet: a strong initialization, not a finished product.

## Step 3: Run a Simulation Environment

LeRobot includes simulation environments for testing:

```python
import gymnasium as gym
from lerobot.common.envs.factory import make_env

# Create the ALOHA transfer cube environment
env = make_env(
    env_name="aloha",
    task_name="AlohaTransferCube-v0",
    obs_type="pixels",
)

obs, info = env.reset()
print(f"Observation shape: {obs['pixels']['top'].shape}")
print(f"Action space: {env.action_space}")
```

The included environments — ALOHA, PushT, XArm — are intentionally small. They are there so you can validate that your policy and data pipeline run end-to-end before you spend hours on a custom task. For larger-scale parallel simulation, see our [sim-to-real transfer guide](/posts/sim-to-real-transfer-guide/), which covers when to graduate from gym-style envs to GPU-batched simulators like Isaac Lab.

## Step 4: Collect Demonstration Data

For real robot learning, you need demonstration data. LeRobot provides tools for this:

```python
from lerobot.common.datasets.lerobot_dataset import LeRobotDataset

# Load an existing dataset to understand the format
dataset = LeRobotDataset("lerobot/aloha_sim_transfer_cube_human")

print(f"Number of episodes: {dataset.num_episodes}")
print(f"Number of frames: {dataset.num_frames}")
print(f"Features: {list(dataset.features.keys())}")
```

For collecting your own data with a real robot, LeRobot supports teleoperation recording:

```bash
# Record demonstrations via teleoperation
python lerobot/scripts/control_robot.py record \
    --robot-path lerobot/configs/robot/aloha.yaml \
    --repo-id your-username/your-dataset \
    --num-episodes 50
```

### Understanding the LeRobot dataset format

`LeRobotDataset` stores three things side by side:

1. **Low-dimensional signals** (joint positions, actions, rewards) in **parquet** — fast random access, columnar, queryable.
2. **High-dimensional observations** (camera frames) as **encoded video** — typically AV1 or H.264 — a single 30-fps episode is usually ~1–10 MB on disk versus hundreds of megabytes if stored as raw frames.
3. **Episode metadata** (length, success flag, task description) in a JSON sidecar.

The format is episode-aware: when you index `dataset[i]`, the loader figures out which episode and frame `i` belongs to and decodes only the relevant video chunk. This is what makes 1000-episode datasets practical to train on without exhausting RAM.

If you're comparing data-collection strategies — teleoperation vs. kinesthetic teaching vs. simulation rollouts vs. self-improvement loops — see our [robot data collection methods comparison](/posts/robot-data-collection-methods-2026/).

## Step 5: Train a Policy

LeRobot supports several policy architectures. ACT (Action Chunking with Transformers) is a good starting point:

```bash
python lerobot/scripts/train.py \
    policy=act \
    env=aloha \
    dataset_repo_id=lerobot/aloha_sim_transfer_cube_human \
    training.num_epochs=100 \
    training.batch_size=8
```

Or programmatically:

```python
from lerobot.scripts.train import train
from lerobot.common.policies.act.configuration_act import ACTConfig

config = ACTConfig(
    chunk_size=100,
    n_action_steps=100,
    dim_model=512,
    n_heads=8,
)

train(
    policy_cls=ACTPolicy,
    policy_config=config,
    dataset_repo_id="lerobot/aloha_sim_transfer_cube_human",
    num_epochs=100,
)
```

### Hyperparameters that actually matter

For ACT specifically, these are the knobs you should know before turning anything else:

- **`chunk_size`** — how many actions the transformer predicts in one shot. Larger chunks mean smoother trajectories but slower replanning. 100 is the ALOHA paper default; drop to 50 for faster reactive tasks, raise to 200 for repetitive long-horizon ones.
- **`n_action_steps`** — how many of those predicted actions you actually execute before re-querying. Setting this equal to `chunk_size` is open-loop within a chunk; setting it to ~10–20% of `chunk_size` gives reactive replanning at higher inference cost.
- **`batch_size`** — bottlenecked by VRAM. ACT with 6 cameras at 480×640 typically needs ~12 GB at batch 8.
- **`learning_rate`** — `1e-5` is the safe default for ACT; halve it if loss spikes, double it only if loss is plateauing after epoch 50.

Empirically, more demonstrations beats more epochs — past 100 epochs on a 50-episode dataset, you're almost always overfitting. Collect more data before you train longer.

### Choosing a policy architecture

| Policy | Best for | Compute | Demos needed |
|--------|----------|---------|--------------|
| **ACT** | Bimanual, precise manipulation | Medium | 30–100 |
| **Diffusion Policy** | Long-horizon, multi-modal trajectories | High | 50–200 |
| **VQ-BeT** | Discrete, repeatable subtasks | Low–Medium | 100+ |
| **Pi-0 / OpenVLA** | Generalization to new tasks (zero-shot) | Very high | leverage pretraining |

Start with ACT unless you specifically need multi-modal behavior — see our deep-dives on [diffusion policy](/posts/diffusion-policy-explained/) and [VLA models](/posts/vla-models-compared-2026/) when you're ready to graduate.

## Step 6: Evaluate Your Policy

```python
from lerobot.scripts.eval import eval_policy

results = eval_policy(
    policy_path="outputs/train/act_aloha/checkpoints/last",
    env_name="aloha",
    task_name="AlohaTransferCube-v0",
    num_episodes=50,
)

print(f"Success rate: {results['success_rate']:.1%}")
print(f"Average return: {results['avg_return']:.2f}")
```

Always evaluate on at least 50 episodes — single-digit episode counts have variance wide enough to be meaningless. Track three numbers, not just success rate:

- **Success rate**: did the task complete?
- **Average return**: how cleanly?
- **Time-to-success**: did it dawdle or hesitate?

A policy with 90% success but 3× the time of the demonstrator is usually broken in a subtle way — it found a slow, unrobust solution that happens to work in sim.

## Step 7: Share Your Model

Push your trained policy to the Hugging Face Hub:

```bash
huggingface-cli login
python lerobot/scripts/push_to_hub.py \
    --policy-path outputs/train/act_aloha/checkpoints/last \
    --repo-id your-username/my-robot-policy
```

Pushing publicly costs nothing and gives you free reproducibility — anyone can `from_pretrained("your-username/my-robot-policy")` and verify your numbers. For non-public work, set `--private` and the same workflow still applies.

## Common Issues and Solutions

**Training loss not decreasing:**
- Check your data quality — bad demonstrations produce bad policies. Visualize a few episodes (`dataset.visualize(episode_index=0)`) before trusting the dataset.
- Try reducing the learning rate by 2×; ACT diverges quickly at high LR.
- Ensure observation normalization is correct — wrong image normalization is the single most common cause of stuck training.

**Policy works in sim but fails in real:**
- See our [sim-to-real transfer guide](/posts/sim-to-real-transfer-guide/).
- Collect 10–20 real-world demonstrations and fine-tune from your sim-trained checkpoint — this is almost always faster than retraining from scratch.
- Add domain randomization (lighting, camera pose, friction) earlier in training, not as a last-minute fix.

**Out of memory:**
- Reduce batch size first; gradient accumulation can preserve effective batch size.
- Lower the image resolution at the dataset config level — 240×320 is often enough.
- Try a smaller policy architecture (VQ-BeT or a half-width ACT).

**Inference too slow on real hardware:**
- Increase `n_action_steps` so you re-plan less often.
- Compile the policy with `torch.compile` or export to ONNX for ~2× speedup.
- Consider edge-AI deployment patterns from our [edge AI robot deployment](/posts/edge-ai-robot-deployment/) guide.

## A Recommended Learning Path

If this tutorial is your entry point, here is the order in which the rest of the EAI² library will compound:

1. [Getting Started with Embodied AI in 2026](/posts/getting-started-embodied-ai-2026/) — the big picture
2. **This tutorial** — your first end-to-end policy
3. [Diffusion Policy Explained](/posts/diffusion-policy-explained/) — your second policy class
4. [Sim-to-Real Transfer Guide](/posts/sim-to-real-transfer-guide/) — bridging to real hardware
5. [Reinforcement Learning for Robotics](/posts/reinforcement-learning-robotics-2026/) — when imitation alone isn't enough
6. [VLA Models Compared](/posts/vla-models-compared-2026/) — graduating to foundation policies

## Next Steps

- Fine-tune on your own robot data — this is where 80% of the practical wins live.
- Try different policy architectures (Diffusion Policy, VQ-BeT) on the same dataset and compare honestly.
- Scale up with Isaac Lab for parallel training when single-env training stops being the bottleneck.
- Deploy on real hardware with [ROS 2](/posts/ros2-for-modern-robotics-2026/) — LeRobot policies are pure PyTorch modules, so wrapping them in a ROS node is straightforward.

## Resources

- [LeRobot GitHub](https://github.com/huggingface/lerobot)
- [LeRobot Documentation](https://huggingface.co/docs/lerobot)
- [Pre-trained Models on Hub](https://huggingface.co/lerobot)
- [5 Robot Learning Frameworks You Should Know](/posts/robot-learning-frameworks-2026/)
- [Robot Data Collection Methods Compared](/posts/robot-data-collection-methods-2026/)
