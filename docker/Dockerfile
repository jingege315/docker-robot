ARG PYTORCH="1.5"
ARG CUDA="10.1"
ARG CUDNN="7"
# the version relationship of tensorflow and cuda: https://www.tensorflow.org/install/source_windows
# this is ok for CUDA=10.1, cuDNN=7.4
ARG TENSORFLOW="2.1.0"

FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-runtime


# libgl1-mesa-glx: opencv need it
RUN apt update && apt install -y git build-essential cmake openssh-server vim lsof net-tools iputils-ping cifs-utils curl libgl1-mesa-glx tree screen unzip
# cleaning the cache will make install packages in the future slower
#RUN apt clean
#RUN rm -rf /var/lib/apt/lists/*

RUN conda install -y numpy matplotlib pandas scipy scikit-learn scikit-image pyqt seaborn cython tqdm sympy numba
RUN conda install -y -c conda-forge jupyter_contrib_nbextensions jupytext xgboost
RUN conda install -y -c anaconda psutil jupyter
RUN conda install -y tensorflow-gpu=${TENSORFLOW}
#RUN /opt/conda/bin/conda clean -ya

RUN pip install --no-cache-dir opencv-python flask gevent werkzeug h5py torchsummaryX torchsummary thop efficientnet_pytorch catalyst paramiko albumentations jieba

# install pytorch/fairseq: https://github.com/pytorch/fairseq
RUN git clone https://github.com/pytorch/fairseq.git /workspace/fairseq
# If your clone is slow from github, you can try to clone from gitee (I sync code by hand)
#RUN git clone https://gitee.com/Jingege315/fairseq.git /workspace/fairseq
WORKDIR /workspace/fairseq
RUN pip install --no-cache-dir -e .

# install MMCV
RUN pip install mmcv-full==latest+torch1.5.0+cu101 -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html

# install mmdetection：https://github.com/open-mmlab/mmdetection.git
RUN git clone https://github.com/open-mmlab/mmdetection.git /workspace/mmdetection
# If your clone is slow from github, you can try to clone from gitee (I sync code by hand)
#RUN git clone https://gitee.com/Jingege315/mmdetection.git /workspace/mmdetection
WORKDIR /workspace/mmdetection
ENV FORCE_CUDA="1"
RUN pip install -r requirements/build.txt
RUN pip install --no-cache-dir -e .

# install mmsegmentation：https://github.com/open-mmlab/mmsegmentation
RUN git clone https://github.com/open-mmlab/mmsegmentation.git /workspace/mmsegmentation
# If your clone is slow from github, you can try to clone from gitee (I sync code by hand)
#RUN git clone https://gitee.com/Jingege315/mmsegmentation.git /workspace/mmsegmentation
WORKDIR /workspace/mmsegmentation
RUN pip install --no-cache-dir -e .

# install jupyter tools
RUN jupyter contrib nbextension install --user
RUN jupyter notebook --generate-config
RUN echo 'c.NotebookApp.contents_manager_class="jupytext.TextFileContentsManager"' >~/.jupyter/jupyter_notebook_config.py
RUN echo 'c.ContentsManager.default_jupytext_formats = ".ipynb,.Rmd"' >~/.jupyter/jupyter_notebook_config.py

WORKDIR /