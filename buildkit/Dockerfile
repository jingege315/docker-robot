# syntax = docker/dockerfile:experimental

ARG PYTORCH="1.7.0"
ARG CUDA="11.0"
ARG CUDNN="8"
# the version relationship of tensorflow and cuda: https://www.tensorflow.org/install/source_windows
# this is ok for CUDA=11.0, cuDNN=8.0
ARG TENSORFLOW="2.4.0"
ARG MMCV=1.3.5+torch${PYTORCH}+cu110

FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-runtime

ARG PYTORCH
ARG CUDA
ARG CUDNN
ARG TENSORFLOW
ARG MMCV

ARG USE_GITEE
ENV GIT=${USE_GITEE:+gitee}
ENV GIT=${GIT:-github}
RUN echo clone git repos from ${GIT}

# libgl1-mesa-glx: opencv need it
RUN --mount=type=cache,target=/var/cache/apt,id=cache_apt,sharing=locked apt update
RUN --mount=type=cache,target=/var/cache/apt,id=cache_apt,sharing=locked apt install -y git build-essential cmake openssh-server vim lsof net-tools iputils-ping cifs-utils curl libgl1-mesa-glx tree screen unzip

RUN --mount=type=cache,target=/opt/conda/pkgs,id=cache_conda conda install -y numpy matplotlib pandas scipy scikit-learn scikit-image pyqt seaborn cython tqdm sympy numba
RUN --mount=type=cache,target=/opt/conda/pkgs,id=cache_conda conda install -y -c conda-forge jupyter_contrib_nbextensions jupytext xgboost
RUN --mount=type=cache,target=/opt/conda/pkgs,id=cache_conda conda install -y -c anaconda psutil jupyter

RUN --mount=type=cache,target=/root/.cache/pip,id=cache_pip pip install opencv-python flask gevent werkzeug h5py torchsummaryX torchsummary thop efficientnet_pytorch catalyst paramiko albumentations jieba openpyxl lightgbm tables pytorch-lightning pytorch-forecasting pyarrow
RUN --mount=type=cache,target=/root/.cache/pip,id=cache_pip pip install tensorflow-gpu==${TENSORFLOW}

# install PyTorchViz tool
# https://github.com/szagoruyko/pytorchviz
RUN --mount=type=cache,target=/var/cache/apt,id=cache_apt,sharing=locked apt install -y graphviz
RUN --mount=type=cache,target=/root/.cache/pip,id=cache_pip pip install torchviz

# install pytorch/fairseq: https://github.com/pytorch/fairseq
RUN --mount=type=cache,target=/cache_git,id=cache_git \
    if [ ! -d /cache_git/${GIT}/fairseq ] ; then \
        echo clone fairseq repo ; \
        if [ -z "$USE_GITEE" ] ; then \
            git clone https://github.com/pytorch/fairseq.git /cache_git/${GIT}/fairseq ; \
        else \
            git clone https://gitee.com/Jingege315/fairseq.git /cache_git/${GIT}/fairseq ; \
        fi ; \
    fi ; \
    cp /cache_git/${GIT}/fairseq /workspace/fairseq -r
WORKDIR /workspace/fairseq
RUN git checkout ee0d5a0f65a25e5f5372776402aac5cb9c4adbf1
RUN pip install --no-cache-dir -e .

# install MMCV
RUN --mount=type=cache,target=/root/.cache/pip,id=cache_pip pip install mmcv-full==${MMCV} -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html

# install mmdetection：https://github.com/open-mmlab/mmdetection.git
RUN --mount=type=cache,target=/cache_git,id=cache_git \
    if [ ! -d /cache_git/${GIT}/mmdetection ] ; then \
        echo clone mmdetection repo ; \
        if [ -z "$USE_GITEE" ] ; then \
            git clone https://github.com/open-mmlab/mmdetection.git /cache_git/${GIT}/mmdetection ; \
        else \
            git clone https://gitee.com/Jingege315/mmdetection.git /cache_git/${GIT}/mmdetection ; \
        fi ; \
    fi ; \
    cp /cache_git/${GIT}/mmdetection /workspace/mmdetection -r
WORKDIR /workspace/mmdetection
# 2.13.0
RUN git checkout a8c681d2854ecdb059fa7ddc12d786dca744ccfb
ENV FORCE_CUDA="1"
RUN pip install -r requirements/build.txt
RUN pip install --no-cache-dir -e .

# install mmsegmentation：https://github.com/open-mmlab/mmsegmentation
RUN --mount=type=cache,target=/cache_git,id=cache_git \
    if [ ! -d /cache_git/${GIT}/mmsegmentation ] ; then \
        echo clone mmsegmentation repo ; \
        if [ -z "$USE_GITEE" ] ; then \
            git clone https://github.com/open-mmlab/mmsegmentation.git /cache_git/${GIT}/mmsegmentation ; \
        else \
            git clone https://gitee.com/Jingege315/mmsegmentation.git /cache_git/${GIT}/mmsegmentation ; \
        fi ; \
    fi ; \
    cp /cache_git/${GIT}/mmsegmentation /workspace/mmsegmentation -r
WORKDIR /workspace/mmsegmentation
RUN git checkout 0df07ab11f8d042751b3a86425ec9a098bfa8dcb
RUN pip install --no-cache-dir -e .

# install jupyter tools
RUN jupyter contrib nbextension install --user
RUN jupyter notebook --generate-config
RUN echo 'c.NotebookApp.contents_manager_class="jupytext.TextFileContentsManager"' >~/.jupyter/jupyter_notebook_config.py
RUN echo 'c.ContentsManager.default_jupytext_formats = ".ipynb,.Rmd"' >~/.jupyter/jupyter_notebook_config.py

WORKDIR /
