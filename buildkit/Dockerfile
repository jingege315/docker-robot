# syntax = docker/dockerfile:experimental

# https://hub.docker.com/r/pytorch/pytorch/tags
ARG PYTORCH="1.10.0"
ARG CUDA="11.3"
ARG CUDA_="113"
ARG CUDNN="8"
# the version relationship of tensorflow and cuda: https://www.tensorflow.org/install/source_windows
# this is ok for CUDA=11.0, cuDNN=8.0
ARG TENSORFLOW="2.6.0"
ARG MMCV="1.3.18"
ARG FAIRSEQ="v0.10.2"
# https://mmdetection.readthedocs.io/en/v2.19.0/get_started.html
ARG MMDETECTION="v2.19.0"
# https://github.com/open-mmlab/mmsegmentation/blob/master/docs/get_started.md#installation
ARG MMSEGMENTATION="v0.19.0"

FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-runtime

ARG PYTORCH
ARG CUDA
ARG CUDA_
ARG CUDNN
ARG TENSORFLOW
ARG MMCV
ARG FAIRSEQ
ARG MMDETECTION
ARG MMSEGMENTATION

ARG USE_GITEE
ENV GIT=${USE_GITEE:+gitee}
ENV GIT=${GIT:-github}
RUN echo clone git repos from ${GIT}

# libgl1-mesa-glx: opencv need it
RUN --mount=type=cache,target=/var/cache/apt,id=cache_apt,sharing=locked apt update
RUN --mount=type=cache,target=/var/cache/apt,id=cache_apt,sharing=locked apt install -y git build-essential cmake openssh-server vim lsof net-tools iputils-ping cifs-utils curl libgl1-mesa-glx tree screen unzip

RUN --mount=type=cache,target=/opt/conda/pkgs,id=cache_conda conda install -y numpy matplotlib pandas scipy scikit-learn scikit-image pyqt seaborn cython tqdm sympy numba
RUN --mount=type=cache,target=/opt/conda/pkgs,id=cache_conda conda install -y -c anaconda psutil jupyter
RUN --mount=type=cache,target=/opt/conda/pkgs,id=cache_conda conda install -y -c conda-forge jupyter_contrib_nbextensions jupytext xgboost

RUN --mount=type=cache,target=/root/.cache/pip,id=cache_pip pip install flask gevent werkzeug h5py thop paramiko jieba openpyxl lightgbm tables pyarrow
RUN --mount=type=cache,target=/root/.cache/pip,id=cache_pip pip install torchsummaryX torchsummary efficientnet_pytorch catalyst albumentations pytorch-lightning pytorch-forecasting
RUN --mount=type=cache,target=/root/.cache/pip,id=cache_pip pip install opencv-contrib-python
RUN --mount=type=cache,target=/root/.cache/pip,id=cache_pip pip install pytest xdoctest
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
    cd /cache_git/${GIT}/fairseq ; git fetch --all --tags ; cd / ; \
    cp /cache_git/${GIT}/fairseq /workspace/fairseq -r
WORKDIR /workspace/fairseq
RUN git checkout ${FAIRSEQ}
RUN pip install --no-cache-dir -e .

# install MMCV
RUN --mount=type=cache,target=/root/.cache/pip,id=cache_pip pip install mmcv-full==${MMCV} -f https://download.openmmlab.com/mmcv/dist/cu${CUDA_}/torch${PYTORCH}/index.html

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
    cd /cache_git/${GIT}/mmdetection ; git fetch --all --tags ; cd / ; \
    cp /cache_git/${GIT}/mmdetection /workspace/mmdetection -r
WORKDIR /workspace/mmdetection
RUN git checkout ${MMDETECTION}
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
    cd /cache_git/${GIT}/mmsegmentation ; git fetch --all --tags ; cd / ; \
    cp /cache_git/${GIT}/mmsegmentation /workspace/mmsegmentation -r
WORKDIR /workspace/mmsegmentation
RUN git checkout ${MMSEGMENTATION}
RUN pip install --no-cache-dir -e .

# install jupyter tools
RUN jupyter contrib nbextension install --user
RUN jupyter notebook --generate-config
RUN echo 'c.NotebookApp.contents_manager_class="jupytext.TextFileContentsManager"' >~/.jupyter/jupyter_notebook_config.py
RUN echo 'c.ContentsManager.default_jupytext_formats = ".ipynb,.Rmd"' >~/.jupyter/jupyter_notebook_config.py

WORKDIR /
