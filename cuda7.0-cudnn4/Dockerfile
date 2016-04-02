FROM bethgelab/jupyter-scipyserver:cuda7.0-cudnn4

MAINTAINER Bethge Lab <opensource@bethgelab.org>

# Install Caffe
# -------------

RUN CUDA_REPO_PKG=cuda-repo-ubuntu1404_7.5-18_amd64.deb && \
    wget http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1404/x86_64/$CUDA_REPO_PKG && \
    sudo dpkg -i $CUDA_REPO_PKG

RUN ML_REPO_PKG=nvidia-machine-learning-repo_4.0-2_amd64.deb && \
    wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1404/x86_64/$ML_REPO_PKG && \
    sudo dpkg -i $ML_REPO_PKG

ENV CAFFE_VERSION 0.14
LABEL com.nvidia.caffe.version="0.14"

# see https://github.com/NVIDIA/nvidia-docker/blob/master/ubuntu-14.04/caffe/Dockerfile
ENV CAFFE_PKG_VERSION 0.14.2-1
RUN apt-get update && apt-get install -y --no-install-recommends --force-yes \
            caffe-nv=$CAFFE_PKG_VERSION \
            caffe-nv-tools=$CAFFE_PKG_VERSION \
            python-caffe-nv=$CAFFE_PKG_VERSION && \
    rm -rf /var/lib/apt/lists/*

ADD requirements.txt /tmp/requirements.txt
RUN pip2 install --no-cache-dir -r /tmp/requirements.txt
RUN pip3 install --no-cache-dir -r /tmp/requirements.txt

# Install TensorFlow GPU version
# ------------------------------
ENV TENSORFLOW_VERSION 0.7.0
RUN pip2 --no-cache-dir install \
    http://storage.googleapis.com/tensorflow/linux/gpu/tensorflow-${TENSORFLOW_VERSION}-py2-none-linux_x86_64.whl

