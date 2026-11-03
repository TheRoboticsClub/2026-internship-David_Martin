---
title: "Internship Progress Week 6"
date: 2026-11-03
math: true
categories: [Internship 2026, Progress]
tags: [imitation-learning, ros2, nav2, dataset, validation, attention]
---

# Internship Progress Week 6

This week, I updated the expert navigation setup, collected a new dataset and trained two versions of the local trajectory prediction network.

## Navigation setup

I reorganized the project into `ImitationLearning4LocalPlanner/ros2_ws`, updated the launch files and documented the ROS 2 Lyrical, Gazebo and Pixi setup.

The navigation configuration now uses longitudinal motion and rotation, with no lateral velocity commands (`vy = 0`). After testing DWB and Rotation Shim, I switched to **Regulated Pure Pursuit (RPP)** to address the observed tracking and orientation problems. NavFn with Dijkstra remains the global planner.

The main settings are:

- Desired forward velocity: **1.0 m/s**, with reversing disabled in RPP.
- Lookahead distance: **2.0 m**, with rotation-to-heading enabled.
- Goal tolerances: **0.3 m** and **0.2 rad**, with `path_length_tolerance` set to **2.0 m**.
- `PoseProgressChecker`, which accounts for rotation as well as translation.

I also corrected the odometry-to-TF bridge, removing an extra 90-degree rotation and consistently using `base_link` in Nav2. This improved the alignment between LiDAR observations and the map. Recovery commands were remapped to `/autonomous_car/cmd_vel` using `Twist`, and a backup manoeuvre was successfully tested. Reversing remains available for recovery behaviours.

## New dataset

With the corrected setup, I recorded **10 navigation episodes**. The processing script removes waiting periods between goals, excludes problematic sections in episodes 3 and 5, and rejects samples with stale or incomplete inputs. A sample is also excluded if its future trajectory crosses a cut or an episode boundary.

The resulting dataset contains **4,480 samples**, sampled at 5 Hz:

| Training | Validation | Test |
|---:|---:|---:|
| 3,055 | 735 | 690 |

The split is made by episode, and normalization statistics are calculated only from training data.

Each input contains LiDAR ranges, ten relative global-plan waypoints and the measured velocities `(v, w)`, where `v = vx`. The output contains **five relative `(x, y)` points at 1, 2, 3, 4 and 5 seconds**, reconstructed from future odometry. The network predicts a local trajectory rather than actuator commands.

## Network training and results

I implemented an adaptation of the local planner from [Pokle et al., *Deep Local Trajectory Replanning and Control for Robot Navigation*](https://iliad.stanford.edu/pdfs/publications/pokle2019deep.pdf). It combines separate LiDAR, global-plan and odometry branches through attention. The pedestrian branch and learned velocity controller are omitted, and the output contains only `(x, y)` coordinates.

In the first version, attention collapsed almost entirely onto LiDAR. For **v2**, I added LayerNorm to the three feature vectors and initialized attention uniformly. I trained both versions on my GPU, selecting the checkpoint with the lowest validation MSE.

| Model | Validation ADE | Validation FDE | Episode 10 ADE | Episode 10 FDE |
|---|---:|---:|---:|---:|
| v1 | 0.259 m | 0.493 m | 0.388 m | 0.659 m |
| v2 | **0.120 m** | **0.202 m** | **0.240 m** | **0.406 m** |

ADE measures the average distance error across the five predicted points; FDE measures the error at five seconds. The best v2 checkpoint was saved at **epoch 20**. Its validation attention weights averaged approximately **65% LiDAR, 33% global plan and 3% odometry**, so attention no longer concentrated exclusively on LiDAR. These weights alone do not establish how much each input affects predictions.

## Next steps

Next, I will check whether changing the global plan and velocity inputs meaningfully changes the predictions, collect new held-out routes, and test the predicted trajectories with a conventional controller in simulation.

The current results are offline measurements from one map. Since episode 10 has already been inspected for both versions, a fresh test set is needed for an independent final evaluation. Closed-loop tests will assess goal completion, collisions and blocking behaviour.