# robot

Let's build an easy to use docker image for machine learning fans.

I am a novice, so there is any error which includes but not limited to code error or English expression errors, please let me know.

Welcome to PRs!

what packages in this docker image?

* CUDA(version=11.3)
* cuDNN(version=8)
* PyTorch(version=1.10)
* TensorFlow(version=2.6.0)
* some open source toolboxes based on PyTorch: Fairseq(version=0.10.2), MMCV(version=1.3.18), MMDetection(version=2.19.0), MMSegmentation(version=0.19.0)
* latest packages from apt: git build-essential cmake openssh-server vim lsof net-tools iputils-ping cifs-utils curl tree screen unzip
* latest packages from conda: numpy matplotlib pandas scipy scikit-learn scikit-image pyqt seaborn cython tqdm sympy numba jupyter_contrib_nbextensions jupytext xgboost psutil jupyter
* latest packages from pip: opencv-python flask gevent werkzeug h5py torchsummaryX torchsummary thop efficientnet_pytorch catalyst paramiko albumentations jieba openpyxl lightgbm tables pytorch-lightning pytorch-forecasting PyTorchViz pyarrow

# how to use

## get docker image

You can generate image from `Dockerfile` or download image from [Docker Hub](https://hub.docker.com/).

### generate image from `Dockerfile` by docker

Maybe build will take some time.

```bash
git clone https://github.com/jingege315/robot.git
cd robot
docker image build -t jingege315/robot:0.3 docker
```

If your clone is slow from github, you can try to clone from gitee (I sync code by hand).

```bash
git clone https://github.com/jingege315/robot.git
cd robot
docker image build -t jingege315/robot:0.3 docker --build-arg USE_GITEE=1
```

### generate image from `Dockerfile` by BuildKit

Maybe build will take some time.

```bash
git clone https://github.com/jingege315/robot.git
cd robot
DOCKER_BUILDKIT=1 docker image build -t jingege315/robot:0.3 buildkit
```

If your clone is slow from github, you can try to clone from gitee (I sync code by hand).

```bash
git clone https://github.com/jingege315/robot.git
cd robot
DOCKER_BUILDKIT=1 docker image build -t jingege315/robot:0.3 buildkit --build-arg USE_GITEE=1
```

### download image from Docker Hub

This is simplest way to get the image.

```bash
docker pull jingege315/robot:0.3
```

## generate container

the command to generate container from this docker image (see more reference [here](https://docs.docker.com/engine/reference/commandline/container_create/)):

```bash
nvidia-docker container run -dit -v <path_host>:<path_container> --name robot_container -p <port_host>:<port_container> --ipc=host jingege315/robot:0.3
```

# more

## start ssh service

The ssh service in container is not running by default, so you should start ssh service after generating container or starting container.

check ssh service status:

```bash
docker container exec -it robot_container service ssh status
```

start ssh service status:

```bash
docker container exec -it robot_container service ssh start
```

You can add you public key into `~/.ssh/authorized_keys` for login without password

```bash
docker container exec -it robot_container mkdir -p ~/.ssh
docker container exec -it robot_container sh -c "echo 'ssh-rsa XXX' >>~/.ssh/authorized_keys"
docker container exec -it robot_container service ssh restart
```

## start jupyter

If you want to use jupyter, it is necessary to remember to add a idle port when you generate this container.

```
docker container exec -it robot_container jupyter notebook /project/kayotype_augment_code --port <some_port> --ip 0.0.0.0 --allow-root --no-browser
```

# version

## 0.1

- add some machine learning framework: PyTorch(version=1.5) and TensorFlow(version=2.1.0).
- add some open source toolboxes based on PyTorch: Fairseq, MMCV, MMDetection, MMSegmentation.
- add many useful python packages as many as possible.

## 0.2

Many package versions have been upgraded.

| package name | version 0.1 | version 0.2 |
| ------------ | ----------- | ----------- |
| CUDA         | 10.1        | 11.0        |
| cuDNN        | 7           | 8           |
| PyTorch      | 1.5         | 1.7         |
| TensorFlow   | 2.1.0       | 2.4.0       |

The PyTorch of this version successfully runs on GeForce RTX 3090.

## 0.3

* **BuildKit can be used to generate image.**
* add package: openpyxl lightgbm tables pytorch-lightning pytorch-forecasting.
* add USE_GITEE flag into Dockerfile. 

## 0.4

Many package versions have been upgraded.

**Fix the version of Fairseq, MMCV, MMDetection and MMSegmentation.**

| package name   | version 0.1 | version 0.2 | version 0.4 |
| -------------- | ----------- | ----------- | ----------- |
| CUDA           | 10.1        | 11.0        | 11.3        |
| cuDNN          | 7           | 8           | 8           |
| PyTorch        | 1.5         | 1.7         | 1.10        |
| TensorFlow     | 2.1.0       | 2.4.0       | 2.6.0       |
| Fairseq        |             |             | 0.10.2      |
| MMCV           |             |             | 1.3.18      |
| MMDetection    |             |             | 2.19.0      |
| MMSegmentation |             |             | 0.19.0      |

