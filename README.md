# Knee-Limited Humanoid Locomotion in Isaac Lab (H1 Knee80)

This project is a small, targeted modification to NVIDIA Isaac Lab’s Unitree H1 locomotion task to simulate a **gait impairment** via **reduced knee flexion range-of-motion (ROM)** and train a policy using reinforcement learning (PPO) that can still walk **stably** under that constraint.

## Demo

![Knee80 gait demo](./PPO_gait_improvement.gif)

---

## What this project does

- Adds a new locomotion environment variant that **clamps the H1 knee joint upper limit to ~80°** (knee-impaired ROM).
- Updates rewards to encourage stable locomotion under restricted ROM.
- Trains a new RL policy (Isaac Lab + `rsl_rl`) that walks **slower but stable** under the constraint.

This is intended as a starting point for:
- impairment-aware locomotion,
- policy adaptation under morphology constraints,
- assistive control / “exo torque” style additions (next step).

---

## Results (qualitative)

The demo shows:
1) **Baseline** policy walking with normal ROM  
2) **Failure case**: applying Knee80 constraint to the baseline policy causes instability/falls  
3) **Recovered gait**: a policy trained under Knee80 walks **stably**, typically at a reduced speed

GIF: https://imgur.com/a/Lxfvi7R

---

## Key changes / where the code lives

### Custom environment (knee-limited variant)
- `source/isaaclab_tasks/isaaclab_tasks/manager_based/locomotion/velocity/config/h1/flat_env_cfg_knee80.py`  
  Defines the knee-impaired env (80° limit) and applies the joint limit constraint during reset/startup.

### Task registration
- `source/isaaclab_tasks/isaaclab_tasks/manager_based/locomotion/velocity/config/h1/__init__.py`  
  Registers the new Gym task ID (e.g., `Isaac-Velocity-Flat-H1-Knee80-v0`).

### Reward changes
- `source/isaaclab_tasks/isaaclab_tasks/manager_based/locomotion/velocity/mdp/rewards.py`  
  Reward modifications to stabilize locomotion under constrained knee ROM.

### Training / playback
- Training: `scripts/reinforcement_learning/rsl_rl/train.py`
- Playback: `scripts/reinforcement_learning/rsl_rl/play.py`
- Sim2Sim transfer: `scripts/sim2sim_transfer/rsl_rl_transfer.py`

---

## Requirements

- NVIDIA Isaac Sim + Isaac Lab installed and working
- GPU strongly recommended
- `rsl_rl` training pipeline (used by standard H1 locomotion tasks)

If you can already run the Isaac Sim Humanoid Policy demo, you’re most of the way there.

---

## Environment: Knee80 constraint

This project simulates impairment by reducing knee flexion ROM.

- Knee upper limit: **80°**  
- Isaac Lab uses **radians** internally:
  - `80° = 1.3962634 rad`

The Knee80 task enforces this constraint via the environment config (not just via GUI edits), ensuring it applies consistently during training.

---

## How to train

From the Isaac Lab repo root (your fork):

```bash
python scripts/reinforcement_learning/rsl_rl/train.py --task <your-knee80-task-name> --headless
