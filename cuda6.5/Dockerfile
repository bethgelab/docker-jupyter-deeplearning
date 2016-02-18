FROM bethgelab/jupyter-scipyserver:cuda6.5

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
RUN pip2 install -r /tmp/requirements.txt

# Install Torch
# -------------
# including Lua, iTorch, loadcaffe, fblualib

RUN apt-get update -qq && DEBIAN_FRONTEND=noninteractive apt-get install -yq --no-install-recommends \
    autoconf \
    autoconf-archive \
    automake \
    binutils-dev \
    bison \
    cmake \
    flex \
    g++ \
    libboost-all-dev \
    libdouble-conversion-dev \
    libedit-dev \
    libevent-dev \
    libgflags-dev \
    libgoogle-glog-dev \
    libiberty-dev \
    libjemalloc-dev \
    libkrb5-dev \
    liblz4-dev \
    liblzma-dev \
    libmatio-dev \
    libnuma-dev \
    libprotobuf-dev \
    libsasl2-dev \
    libsnappy-dev \
    libssl-dev \
    libssl-dev \
    libtool \
    pkg-config \
    protobuf-compiler \
    python-zmq \
    torch7-nv \
    unzip \
  && rm -rf /var/lib/apt/lists/*

# workaround
RUN ln -s /usr/lib/x86_64-linux-gnu/libTH* /usr/lib/

RUN git clone https://github.com/facebook/iTorch.git ~/itorch \
  && cd ~/itorch \
  && luarocks make \
  && cp -r /root/.ipython/* /usr/local/share/jupyter/

RUN luarocks install loadcaffe

RUN mkdir /tmp/build_fblualib \
  && cd /tmp/build_fblualib \
  && git clone -b v0.35.0 --depth 1 https://github.com/facebook/folly.git \
  && git clone -b v0.24.0 --depth 1 https://github.com/facebook/fbthrift.git \
  && git clone https://github.com/facebook/thpp \
  && git clone https://github.com/facebook/fblualib

RUN cd /tmp/build_fblualib/folly/folly \
  && autoreconf -ivf \
  && ./configure \
  && make -j`nproc` \
  && sudo make install \
  && sudo ldconfig

RUN cd /tmp/build_fblualib/fbthrift/thrift \
  && autoreconf -ivf \
  && ./configure \
  && make -j`nproc` \
  && sudo make install

RUN cd /tmp/build_fblualib/thpp/thpp \
  && ./build.sh \
  && cd /tmp/build_fblualib/fblualib/fblualib \
  && ./build.sh \
  && ldconfig \
  && cd / \
  && rm -rf /tmp/build_fblualib


# Install TensorFlow GPU version
# ------------------------------
ENV TENSORFLOW_VERSION 0.7.0
RUN pip2 --no-cache-dir install \
    http://storage.googleapis.com/tensorflow/linux/gpu/tensorflow-${TENSORFLOW_VERSION}-py2-none-linux_x86_64.whl

