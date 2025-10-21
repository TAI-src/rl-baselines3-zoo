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

Warnings about using gym instead of gymnasium are expected and can be ignored for now.
