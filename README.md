# ROS2 Multi-DAG Soft Real-Time Scheduler (RPExecutor)

**Author:** Md Mehedi Hasan  
**GitHub:** [Mehedi16009](https://github.com/Mehedi16009)  
**Docker-based reproducible workspace for ARM64/macOS and Linux**

---

## Project Overview

This repository implements a **Soft Real-Time Scheduling Framework for Multi-DAG ROS2 Systems**, featuring:

- **RPExecutor:** Custom ROS2 executor with **restricted parallelism per DAG**, EDF-style soft-deadline scheduling, and detailed runtime instrumentation.
- **Multi-DAG Demo:** Simulated nodes representing LiDAR, Camera, Perception, Detection, Planning, Tracking, and Control pipelines.
- **Stress Testing & Tuning:** Automated stress tests, tuning iterations, and cross-configuration sweeps to validate scheduler performance under various load conditions.
- **Docker-Only Workspace:** Ensures reproducibility across platforms (ARM64, Linux, macOS) without requiring native ROS2 installation.

---

## Repository Structure
```
rp_ws/
├── src/
│   ├── rp_executor/
│   │   ├── include/rp_executor/
│   │   ├── src/rp_executor.cpp
│   │   └── CMakeLists.txt
│   └── multi_dag_demo/
│       ├── src/
│       ├── include/multi_dag_demo/
│       └── CMakeLists.txt
├── phase12_stress.log
├── phase15_sweep.sh
├── phase16_run_best.sh
├── final_report.md
├── cross_configuration_table.csv
├── best_worst_configs.csv
├── REPRODUCE.md
└── .gitignore
```
---

## Prerequisites

- **Docker** installed (tested with Docker Desktop on macOS M1/ARM64)
- Minimum **8GB RAM** recommended
- **Bash shell** or compatible terminal
- Optional: `git` for cloning repository

---

## Getting Started

1. **Clone the repository:**

```
bash
git clone https://github.com/Mehedi16009/ros2_multi_dag_rp_scheduler.git
cd ros2_multi_dag_rp_scheduler/rp_ws
```
2.	Start Docker container with ARM64 ROS2 Humble:
   ```
docker run -it --rm \
    -v $PWD:/root/rp_ws \
    -w /root/rp_ws \
    arm64v8/ros:humble
```
3.	Set up ROS2 workspace inside container:
```
source /opt/ros/humble/setup.bash
colcon build
source install/setup.bash
```
Running the Demo
	•	Timer-based Multi-DAG demo:
  ```
ros2 run multi_dag_demo multi_dag_demo_main
```
•	With RPExecutor enabled:
```
export RP_EXECUTOR_MAX_ACTIVE_DAG1=3
export RP_EXECUTOR_MAX_ACTIVE_DAG2=5
ros2 run multi_dag_demo multi_dag_demo_main
```
•	Enable trace logs for debugging / stress tests:
```
export RP_EXECUTOR_TRACE=1
ros2 run multi_dag_demo multi_dag_demo_main
```

---

Stress Testing & Tuning:
```
Automated tuning and stress runs are available:
	•	Phase 12 Stress Test: phase12_stress.log
	•	Phase 15 Configuration Sweep: phase15_sweep.sh
	•	Phase 16 Best Config Run: phase16_run_best.sh

Default tuned parameters (Phase 16):
	•	Max active DAG slots: DAG1 = 3, DAG2 = 5
	•	Executor threads: 8
	•	Deadline scale: 1.1 for all DAGs
	•	Sensor timers: LiDAR = 20ms, Camera = 30ms
```	
  ---

  Artifacts & Analysis:
```
    •	Logs: phase12_stress.log, phase15/logs/
	•	Summary tables: cross_configuration_table.csv, best_worst_configs.csv, log_extracted_metrics.csv
	•	Graphs: threads_vs_avg_combined_miss.png, max_active_vs_avg_miss.png, etc.
	•	Final report: final_report.md
	•	Reproducibility instructions: REPRODUCE.md
```
All artifacts allow full reproduction of stress tests, tuning sweeps, and performance analysis.

---

Notes:
```
	•	Docker-only workflow ensures reproducibility without local ROS2 installation.
	•	ARM64 tested: macOS M1/Apple Silicon; works on Linux ARM64.
	•	Thread pool and DAG concurrency are configurable via environment variables (RP_EXECUTOR_*).
	•	EDF-style soft-deadline scheduler handles multiple DAGs with per-DAG execution limits and detailed runtime instrumentation.
```
---

Citation / Usage

If using this framework for research, please cite:

Md Mehedi Hasan. Soft Real-Time Scheduling Framework for Multi-DAG ROS2 Systems. GitHub repository, 2026. https://github.com/Mehedi16009/ros2_multi_dag_rp_scheduler￼


---

