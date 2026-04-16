---
title: "LeRobot Tutorial: Collect Data and Train Your First Robot Policy"
date: 2026-04-07
draft: false
tags: ["LeRobot", "tutorial", "imitation learning", "Hugging Face", "hands-on"]
categories: ["Tools & Frameworks"]
series: ["From Zero to Robot Engineer"]
weight: 4
summary: "A step-by-step tutorial for using Hugging Face LeRobot to collect demonstration data, train an imitation learning policy, and deploy it on a simulated robot."
ShowToc: true
TocOpen: true
---

LeRobot is Hugging Face's open-source framework for robot learning. It standardizes the entire pipeline from data collection to deployment. This tutorial walks you through the complete workflow.

## Prerequisites

- Python 3.10+
- A machine with at least 8GB RAM
- Basic familiarity with PyTorch

## Step 1: Installation

```bash
pip install lerobot

# Verify installation
python -c "import lerobot; print(lerobot.__version__)"
```

## Step 2: Explore Pre-trained Policies

LeRobot hosts pre-trained policies on the Hugging Face Hub:

```python
from lerobot.common.policies.act.modeling_act import ACTPolicy
from huggingface_hub import snapshot_download

# Download a pre-trained ACT policy for ALOHA simulation
policy_path = snapshot_download("lerobot/act_aloha_sim_transfer_cube_human")
```

These policies are trained on standard benchmarks and serve as baselines or starting points for fine-tuning.

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

## Step 7: Share Your Model

Push your trained policy to the Hugging Face Hub:

```bash
huggingface-cli login
python lerobot/scripts/push_to_hub.py \
    --policy-path outputs/train/act_aloha/checkpoints/last \
    --repo-id your-username/my-robot-policy
```

## Common Issues and Solutions

**Training loss not decreasing:**
- Check your data quality — bad demonstrations produce bad policies
- Try reducing the learning rate
- Ensure observation normalization is correct

**Policy works in sim but fails in real:**
- See our [sim-to-real transfer guide](/posts/sim-to-real-transfer-guide/)
- Collect 10-20 real-world demonstrations and fine-tune

**Out of memory:**
- Reduce batch size
- Use image resizing in the dataset configuration
- Try a smaller policy architecture

## Next Steps

- Fine-tune on your own robot data
- Try different policy architectures (Diffusion Policy, VQ-BeT)
- Scale up with Isaac Lab for parallel training
- Deploy on real hardware with ROS 2

## Resources

- [LeRobot GitHub](https://github.com/huggingface/lerobot)
- [LeRobot Documentation](https://huggingface.co/docs/lerobot)
- [Pre-trained Models on Hub](https://huggingface.co/lerobot)
- [5 Robot Learning Frameworks You Should Know](/posts/robot-learning-frameworks-2026/)
