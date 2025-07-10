# Optuna-Fly: Automated Reward Shaping for Robust UAV Intruder Avoidance

This repository contains the complete framework and code for the paper, *"Optuna-Fly: Automated Reward Shaping for Robust UAV Intruder Avoidance."*

The project demonstrates a closed-loop system that uses multi-objective Bayesian optimization to automatically discover robust reinforcement learning policies for a UAV tasked with navigating through dynamic, uncooperative intruder traffic.

---

## ⚙️ Project Structure

```
Optuna-Fly/
├── gym_pathfinding/      # Custom Gymnasium environment package
│   ├── envs/
│   │   └── intruder_avoidance_env.py
│   └── __init__.py
├── models/               # Saved model checkpoints
├── optuna_trials/        # Artifacts from Optuna runs (models, logs)
├── plots/                # Generated comparison plots
├── results/              # Evaluation CSV files
├── scripts/              # All executable Python scripts
│   ├── train_optuna_manager.py
│   ├── train_optuna.py
│   ├── evaluate_intruder_for_optuna.py
│   └── plot_intruder_compare_with_baseline.py
├── pbrs_tuning.db        # Optuna study database
├── requirements.txt      # Project dependencies
└── setup.py              # Installation script for the custom environment
```

---

## 🚀 Getting Started

### 1. Clone the Repository

First, clone this repository to your local machine:
```bash
git clone [https://github.com/bakethi/Optuna-Fly.git](https://github.com/bakethi/Optuna-Fly.git)
cd Optuna-Fly
```

### 2. Create and Activate a Python Virtual Environment

It is highly recommended to use a virtual environment to manage dependencies.
```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install Dependencies

Install all required Python libraries from the `requirements.txt` file. Then, install the custom Gymnasium environment in "editable" mode (`-e`), which allows you to make changes to the environment code without reinstalling.
```bash
pip install --upgrade pip
pip install -r requirements.txt
pip install -e .
```
After this step, your environment is registered with Gymnasium and can be called via its ID `gym_pathfinding/IntruderAvoidance-v0`.

---

## 🔬 Reproducing the Optuna Study

The core of this research is the automated hyperparameter optimization study. The following commands allow you to replicate this process.

### 1. Initialize the Study

This one-time command creates the SQLite database that will store all trial results. This prevents a race condition when running multiple workers in parallel.
```bash
optuna create-study --study-name "IntruderAvoidance-PBRS-MultiObjective" --storage "sqlite:///pbrs_tuning.db" --directions "minimize" "minimize"
```

### 2. Run the Optimization

To run the full study, execute the manager script. This will begin the process of proposing parameters, training agents, and evaluating them, running for 200 trials as specified in the script.
```bash
python scripts/train_optuna_manager.py
```

### 3. Monitor the Study with the Optuna Dashboard

While the main study is running (or after it has finished), open a **new terminal window**, activate the virtual environment, and run the following command to launch the web-based dashboard:
```bash
# Make sure you are in the project's root directory
source .venv/bin/activate
optuna-dashboard sqlite:///pbrs_tuning.db
```
Navigate to the URL provided in the terminal (usually `http://127.0.0.1:8080/`) in your web browser to view the study's progress in real-time, including the Pareto front and hyperparameter importance plots.

---

## 📊 Evaluating a Single Trained Model

If you want to evaluate a single, specific agent (e.g., a baseline model or a specific trial from the Optuna study) against the full evaluation gauntlet, you can use the standalone evaluation script.

1.  **Locate the model file** you wish to test (e.g., `models/baseline_average_params/agent_avg.zip`).
2.  **Run the script from the command line**, providing the path to the model and a path for the output CSV log.

```bash
python scripts/evaluate_intruder_for_optuna.py \
    --model_path path/to/your/agent.zip \
    --log_file results/your_model_evaluation.csv
```

This will run the full evaluation and save the detailed results to the specified log file, which can then be used for plotting and analysis.