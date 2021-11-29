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

# libgl1-mesa-glx: opencv need it
RUN apt update && apt install -y git build-essential cmake openssh-server vim lsof net-tools iputils-ping cifs-utils curl libgl1-mesa-glx tree screen unzip

RUN conda install -y numpy matplotlib pandas scipy scikit-learn scikit-image pyqt seaborn cython tqdm sympy numba
RUN conda install -y -c conda-forge jupyter_contrib_nbextensions jupytext xgboost
RUN conda install -y -c anaconda psutil jupyter

RUN pip install --no-cache-dir flask gevent werkzeug h5py thop paramiko jieba openpyxl lightgbm tables pyarrow
RUN pip install --no-cache-dir torchsummaryX torchsummary efficientnet_pytorch catalyst albumentations pytorch-lightning pytorch-forecasting
RUN pip install --no-cache-dir opencv-contrib-python
RUN pip install --no-cache-dir tensorflow-gpu==${TENSORFLOW}

# install PyTorchViz tool
# https://github.com/szagoruyko/pytorchviz
RUN apt install -y graphviz
RUN pip install torchviz

# install pytorch/fairseq: https://github.com/pytorch/fairseq
RUN if [ -z "$USE_GITEE" ] ; then \
    echo clone fairseq from github ; \
    git clone https://github.com/pytorch/fairseq.git /workspace/fairseq ; \
    else \
    echo clone fairseq from gitee ; \
    git clone https://gitee.com/Jingege315/fairseq.git /workspace/fairseq ; \
    fi
WORKDIR /workspace/fairseq
RUN git checkout ee0d5a0f65a25e5f5372776402aac5cb9c4adbf1
RUN pip install --no-cache-dir -e .

# install MMCV
RUN pip install mmcv-full==${MMCV} -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html

# install mmdetection：https://github.com/open-mmlab/mmdetection.git
RUN if [ -z "$USE_GITEE" ] ; then \
    echo clone mmdetection from github ; \
    git clone https://github.com/open-mmlab/mmdetection.git /workspace/mmdetection ; \
    else \
    echo clone mmdetection from gitee ; \
    git clone https://gitee.com/Jingege315/mmdetection.git /workspace/mmdetection ; \
    fi
WORKDIR /workspace/mmdetection
# 2.13.0
RUN git checkout a8c681d2854ecdb059fa7ddc12d786dca744ccfb
ENV FORCE_CUDA="1"
RUN pip install -r requirements/build.txt
RUN pip install --no-cache-dir -e .

# install mmsegmentation：https://github.com/open-mmlab/mmsegmentation
RUN if [ -z "$USE_GITEE" ] ; then \
    echo clone mmsegmentation from github ; \
    git clone https://github.com/open-mmlab/mmsegmentation.git /workspace/mmsegmentation ; \
    else \
    echo clone mmsegmentation from gitee ; \
    git clone https://gitee.com/Jingege315/mmsegmentation.git /workspace/mmsegmentation ; \
    fi
WORKDIR /workspace/mmsegmentation
RUN git checkout 0df07ab11f8d042751b3a86425ec9a098bfa8dcb
RUN pip install --no-cache-dir -e .

# install jupyter tools
RUN jupyter contrib nbextension install --user
RUN jupyter notebook --generate-config
RUN echo 'c.NotebookApp.contents_manager_class="jupytext.TextFileContentsManager"' >~/.jupyter/jupyter_notebook_config.py
RUN echo 'c.ContentsManager.default_jupytext_formats = ".ipynb,.Rmd"' >~/.jupyter/jupyter_notebook_config.py

WORKDIR /
