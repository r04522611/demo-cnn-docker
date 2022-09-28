FROM nvidia/cuda:11.3.1-cudnn8-devel-ubuntu20.04

ENV PATH=/root/.local/bin:$PATH
ENV PYTHONPATH=/root/.local/lib/python3.8/site-packages:$PYTHONPATH
ENV NVIDIA_DRIVER_CAPABILITIES=all
ENV TZ=America/Atlanta
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
ARG DEBIAN_FRONTEND=noninteractive

# add libglvnd support (More info: https://hub.docker.com/r/nvidia/opengl)
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        libglvnd0 \
        libgl1 \
        libglx0 \
        libegl1 \
        libgles2 \
        gcc

# python base
RUN apt-get install -y --no-install-recommends \
        wget \
        ca-certificates \
        python3.8-dev \
        python3-pyqt5.qtsvg \
        python3-distutils \
        python3-apt \
        build-essential \
        manpages-dev \
        git

RUN wget https://bootstrap.pypa.io/get-pip.py -O /tmp/get-pip.py && \
    python3 /tmp/get-pip.py --user

COPY requirements.txt /tmp

# install python modules in /root/.local
RUN pip3 install --user --no-cache-dir -r /tmp/requirements.txt


# install mmdete & mmdetection3d in /root/.local
RUN pip3 install openmim
RUN mim install mmcv-full
# RUN pip3 install pycocotools
RUN git clone https://github.com/open-mmlab/mmdetection.git && cd mmdetection && git checkout v2.24.0
WORKDIR '/mmdetection'
RUN pip3 install -r requirements/build.txt
RUN pip3 install -v -e .

RUN pip3 install mmsegmentation
RUN  cd ..
RUN  cd ..
RUN git clone https://github.com/open-mmlab/mmdetection3d.git && cd mmdetection3d && git checkout v0.13.0
# ENV FORCE_CUDA="1"
# WORKDIR '/mmdetection3d'
# RUN ls
RUN pip install -v -e .
RUN pip3 install cumm-cu113
RUN pip3 install spconv-cu113
RUN pip install nuscenes-devkit

# use the default bashrc provided by Ubuntu & change the color to yellow
RUN cp /etc/skel/.bashrc /etc/bash.bashrc && \
    sed -i 's/#force_color_prompt/force_color_prompt/g' /etc/bash.bashrc && \
    sed -i 's/32m/33m/g' /etc/bash.bashrc && \
    echo "\nalias pylab='ipython --pylab'" >> /etc/bash.bashrc

# make python modules in /root/.local visible to non-root users
RUN find /root -type d -exec chmod 755 {} +

RUN rm -rf /tmp/* /var/lib/apt/lists/*

