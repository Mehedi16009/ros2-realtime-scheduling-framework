# ReDAG$`^{\mathrm{\textbf{RT}}}`$: A Rate-Priority Executor for Multi-DAG Real-Time Execution in ROS 2

Author: Md Mehedi Hasan.<br>
Platform: ROS 2 Humble (Ubuntu 22.04).<br>
Artifact Type: Reproducible Research Artifact.<br>
Execution Environment: Docker (ARM64 and x86_64).

---

## Abstract

ReDAG$`^{\mathrm{\textbf{RT}}}`$ is a middleware-level scheduling framework for multi-DAG robotic workloads executing in ROS 2. Modern robotic systems frequently execute multiple independent Directed Acyclic Graph (DAG) pipelines such as perception, planning, tracking, and control within a shared executor. The default ROS 2 executor model does not enforce a global priority hierarchy across DAG boundaries, which may introduce cross-DAG interference and response-time variability under load.

This artifact provides a structured executor-level scheduling framework and a fully reproducible evaluation environment to enable systematic investigation of multi-DAG scheduling behavior. The implementation includes a custom rate-aware executor, configurable concurrency control, soft-deadline instrumentation, and automated experimental pipelines.

The artifact is designed to support reproducible evaluation of middleware-level scheduling semantics in ROS 2.

---

## 1. Motivation

In ROS 2, callbacks from independent DAG pipelines are scheduled within a shared executor using FIFO ordering or thread availability semantics. No global rate-based priority hierarchy is enforced across DAG boundaries. Under moderate to high utilization, this may result in:
	•	Cross-DAG scheduling interference <br>
	•	Effective priority inversion across pipelines <br>
	•	Increased response-time variance <br>
	•	Degradation in deadline adherence

ReDAG$`^{\mathrm{\textbf{RT}}}`$ provides a structured scheduling abstraction to study these behaviors and evaluate alternative dispatch policies under controlled conditions.

---

## 2. Artifact Objectives

This artifact enables controlled investigation of: <br>
	1.	Cross-DAG interference in shared executors <br>
	2.	Effects of executor-level scheduling policies on response time <br>
	3.	Soft real-time deadline behavior under varying utilization <br> 
	4.	Sensitivity to thread count and per-DAG concurrency limits <br>
	5.	Reproducibility of scheduling experiments across platforms

The focus is executor-level scheduling semantics. Kernel-level real-time guarantees are outside the scope of this artifact.

---

## 3. System Overview

3.1 Execution Model

The workload consists of multiple independent DAG pipelines:
	•	Sensor DAG (e.g., LiDAR, Camera) <br>
	•	Perception / Detection DAG <br>
	•	Planning / Tracking DAG <br>
	•	Control DAG

Each DAG:
	•	Is composed of timer-triggered callbacks <br>
	•	Enforces internal precedence constraints <br>
	•	Shares a common ROS 2 executor

All callbacks compete at the executor dispatch layer.

---

3.2 RPExecutor Design

The custom executor introduces structured scheduling controls: <br>
	•	Per-DAG active callback limits <br>
	•	Rate-aware dispatch hierarchy <br>
	•	Soft-deadline tracking <br>
	•	EDF-style local dispatch within constrained execution regions <br>
	•	Explicit instrumentation hooks <br>
	•	Configurable thread pool size

Collected runtime metrics include:
	•	Per-callback response time <br>
	•	Deadline miss rate <br>
	•	Average and worst-case latency <br>
	•	Combined miss metrics <br>
	•	Thread utilization

The implementation isolates executor-level scheduling behavior from application logic to enable repeatable evaluation.

---

## Repository Structure
```
rp_ws/
├── src/
│   ├── rp_executor/              # Custom executor implementation
│   └── multi_dag_demo/           # Synthetic multi-DAG workload
├── scripts/                      # Automated stress & sweep scripts
├── logs/                         # Generated runtime traces
├── analysis/                     # Extracted metrics and plots
├── final_report.md               # Evaluation summary
├── REPRODUCE.md                  # Step-by-step reproduction guide
└── README.md
```

All experimental artifacts can be regenerated using the provided scripts.

---

## 5. Reproducibility

5.1 Requirements
	•	Docker Desktop or Docker Engine <br>
	•	≥ 8 GB RAM recommended <br>
	•	Linux or macOS host <br>
	•	No native ROS 2 installation required 

---

5.2 Setup

Clone the repository:
```
git clone https://github.com/Mehedi16009/ros2_multi_dag_rp_scheduler.git
cd ros2_multi_dag_rp_scheduler/rp_ws
```

Launch the ROS 2 Humble container:
```
docker run -it --rm \
  -v $PWD:/root/rp_ws \
  -w /root/rp_ws \
  arm64v8/ros:humble
```
Inside the container:
```
source /opt/ros/humble/setup.bash
colcon build
source install/setup.bash
```

---

## 6. Running the Workload

Default Execution
```
ros2 run multi_dag_demo multi_dag_demo_main
```

With RPExecutor Enabled:
```
export RP_EXECUTOR_MAX_ACTIVE_DAG1=3
export RP_EXECUTOR_MAX_ACTIVE_DAG2=5
ros2 run multi_dag_demo multi_dag_demo_main
```
Enable Runtime Instrumentation:
```
export RP_EXECUTOR_TRACE=1
ros2 run multi_dag_demo multi_dag_demo_main
```

---

## 7. Experimental Pipeline

The artifact includes automated evaluation phases:
```
Phase                  Description
Phase 12         High-load stress testing
Phase 15         Configuration sweep across parameters
Phase 16         Validation of best-performing configuration
```

---

Evaluation outputs include: <br>
	•	Raw log traces <br>
	•	Extracted metric tables <br>
	•	Cross-configuration comparison tables <br>
	•	Performance plots <br>
	•	Summary statistics

All experiments can be re-executed using the scripts in the scripts/ directory.

---

## 8. Evaluation Parameters

Key configurable parameters: <br>
	•	Thread pool size <br>
	•	Per-DAG maximum active callbacks <br>
	•	Deadline scaling factor <br>
	•	Sensor timer periods <br>
	•	Instrumentation enable or disable

These parameters enable systematic sensitivity analysis.

---

## 9. Experimental Scope and Limitations

Scope
	•	Middleware-level scheduling behavior <br>
	•	Soft real-time performance evaluation <br>
	•	Executor-level interference analysis

Limitations <br>
	•	Docker-based execution introduces virtualization overhead <br>
	•	No kernel-level real-time scheduling enforcement <br>
	•	No formal schedulability proof <br>
	•	Evaluation limited to a single-host environment

This artifact does not claim hard real-time guarantees.

---

## 10. Expected Observations

Under increased concurrency: <br>
	•	The default executor exhibits higher response-time variance <br>
	•	Deadline miss rate increases with utilization <br>
	•	Executor configuration significantly impacts performance

The RPExecutor enables structured control over concurrency and systematic measurement of scheduling behavior.

---

## 11. Artifact Reproducibility Checklist <br>
	•	Deterministic container environment <br>
	•	Fixed ROS 2 distribution (Humble) <br>
	•	Explicit build instructions <br>
	•	Scripted experimental runs <br>
	•	Version-controlled parameters <br>
	•	Logged raw traces <br>
	•	Published summary tables

---

## 12. Citation

If referencing this artifact: <br>
Md Mehedi Hasan <br>
Email: [mehedi.hasan.ict@mbstu.ac.bd](mehedi.hasan.ict@mbtu.ac.bd) <br>
ReDAG-RT: A Rate-Priority Executor for Multi-DAG Real-Time Execution in ROS 2. <br>
GitHub Research Artifact, 2026. <br>
```https://github.com/Mehedi16009/ros2_multi_dag_rp_scheduler```

