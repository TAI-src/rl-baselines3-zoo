# Anwendungsspezifische Reinforcement-Learning Ansätze

This is the seminar-specific readme. For details on the zoo, please see the [original readme](zoo/README.md).

## Setup Stable-Baselines3-Zoo (Docker)

### Clone the repo (including submodules with trained agents)

```bash
git clone git@github.com:TAI-src/rl-baselines3-zoo.git
cd rl-baselines3-zoo
git submodule update --init --recursive
```

### Setup DISPLAY to allow rendering from within Docker

```bash
export DISPLAY=:0
xhost +local:docker
```

If running withing docker, this needs to be set up so that the video tests are not skipped. Not strictly necessary for running, though.

### Build the Docker image

Build the docker image locally (because the latest tag has not been pushed). This makes sure the docker image version aligns with the code.
Choose between CPU or GPU version.
More details in the [docs](https://stable-baselines3.readthedocs.io/en/master/guide/install.html).

```bash
make docker-cpu
```

### Verify installation

```bash
./scripts/run_docker_cpu.sh pytest tests/test_*.py
```

Runs the existing pytests. Tests should pass.
Warnings about using gym instead of gymnasium are expected and can be ignored for now.

```bash
./scripts/run_docker_cpu.sh bash
python -m rl_zoo3.enjoy
```

This starts an interactive docker container session and runs the enjoy script with default parameters. You should see a window pop up with a rendering of the environment (CartPole-v1).

## Tailoring environments / algorithms (suggested workflow)

### Implement your changes as wrapper(s)

Create a new file in `seminar/wrapper/` where you implement your wrapper. For an example, see [`seminar/wrapper/custom_wrapper.py`](seminar/wrapper/custom_wrapper.py). Make sure to inherit from `gym.Wrapper` or any of its subclasses. For a general documentation on wrappers see the [gymnasium documentation](https://gymnasium.farama.org/api/wrappers/) and for specific examples of wrappers in this repo see the existing wrappers in `rl_zoo3/wrappers/`.

### Create config file

Create a new config file in `seminar/experiments` based on the original hyperparameter config files in the `hyperparams` folder. You can copy the existing settings for `atari` for the algorithm you want to use and modify them as needed. Make sure to specify your custom wrapper in the config file. For an example, see [`seminar/experiments/sample_experiment_a2c.yml`](seminar/experiments/sample_experiment_a2c.yml).

### Run training (inside docker)

You can run training using the provided training scripts. This will make it easiest to plot the results afterwards. For example, to train an A2C agent with your custom wrapper and config file, you can run:

```bash
./scripts/run_docker_cpu.sh bash
python utils/train_custom.py --env PongNoFrameskip-v4 --algo a2c --conf experiments/sample_experiment_a2c.yml --seed 1337
```

This will start training the agent with the specified environment, algorithm, config file, and seed. The trained model and logs will be saved in the `logs/` directory.

### Plot training progress

After training, you can plot the training progress using the provided plotting scripts. For example, to plot the results of your training run, you can run:

```bash
./scripts/run_docker_cpu.sh bash
python -m rl_zoo3.plots.plot_train --algo a2c --env PongNoFrameskip-v4 --exp-folder logs/ --file_name a2c_sample_plot_train.png
```

This will generate a plot of the training progress and save them in the current directory with the given name. It will include multiple results runs if they exist.

## Plot evaluation results

When training with the script, the agents are intermittently evaluated using an EvaluationCallback. The results of these evaluations are saved in the `logs/` directory as well. You can plot these evaluation results using the following command:

```bash
./scripts/run_docker_cpu.sh bash
python -m rl_zoo3.plots.all_plots --algo a2c --env PongNoFrameskip-v4 --exp-folder logs/ --file_name a2c_sample_all_plots.png
```

This will generate a plot including confidence intervals for the evaluation results and save it in the current directory with the given name. It will include multiple results runs if they exist.
