---
title: "Internship Progress Week 4"
date: 2026-10-06
math: true
categories: [Internship 2026, Progress]
tags: [imitation-learning, ros2, nav2, dwb, lidar, dataset]
---

## What I did this week

This week I finished setting up **Nav2 as the expert navigation system** in the local ROS 2 environment.

I configured the `autonomous_car` in **holonomic mode** and used:

- **NavFn** as the global planner,
- **DWB** as the local controller,
- and the LiDAR data converted to **LaserScan** for obstacle detection.

I also tuned the local costmap parameters to improve obstacle avoidance and reduce collisions during navigation.

![Nav2 expert navigation with LiDAR and local costmap](/assets/img/week4_nav2_expert.png)

After validating the expert navigation, I recorded the first demonstration using `ros2 bag`, including:

```text
/plan
/autonomous_car/scan
/autonomous_car/odom
/autonomous_car/cmd_vel
/tf
/tf_static
/clock
```

The recorded demonstration was then processed offline to generate the first supervised dataset.

The preprocessing pipeline includes:

```text
rosbag
   ↓
global plan processing
   ↓
LiDAR processing
   ↓
odometry processing
   ↓
temporal synchronization
   ↓
future trajectory generation
   ↓
dataset
```

For each sample, the current input is:

```text
Global plan: 10 × (x, y)
LiDAR: 360 ranges
Odometry: (v, w)
```

and the output is a future trajectory with 5 poses:

```text
t+1s
t+2s
t+3s
t+4s
t+5s
```

with each pose represented as:

```text
(x, y, cos(yaw), sin(yaw))
```

From the first recorded trajectory, I generated **169 supervised samples**.

## Navigation demo

<video controls width="100%">
  <source src="{{ '/assets/vid/2026-10-06%2000-25-41.mp4' | relative_url }}" type="video/mp4">
  Your browser does not support the video tag.
</video>

## Next steps

The next step is to validate several dataset samples visually and then record many more expert trajectories.

After that, I will start reproducing the neural network architecture from the Stanford paper and train the first Imitation Learning model.
