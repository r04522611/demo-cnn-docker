FROM nvidia/cuda:11.3.1-cudnn8-devel-ubuntu20.04

ENV PATH=/root/.local/bin:$PATH
ENV PYTHONPATH=/root/.local/lib/python3.10/site-packages:$PYTHONPATH
ENV NVIDIA_DRIVER_CAPABILITIES=all

# add libglvnd support (More info: https://hub.docker.com/r/nvidia/opengl)
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        libglvnd0 \
        libgl1 \
        libglx0 \
        libegl1 \
        libgles2

# python base
RUN apt-get install -y --no-install-recommends \
        wget \
        ca-certificates \
        python3 \
        python3-pyqt5.qtsvg \
        gcc-5

RUN wget https://bootstrap.pypa.io/get-pip.py -O /tmp/get-pip.py && \
    python3 /tmp/get-pip.py --user

COPY requirements.txt /tmp

# install python modules in /root/.local
RUN pip3 install --user --no-cache-dir -r /tmp/requirements.txt


# install mmdete & mmdetection3d in /root/.local
RUN pip3 install openmim\
    mim install mmcv-full \
    pip3 install mmdet \
    pip3 install mmsegmentation \
    git clone https://github.com/open-mmlab/mmdetection3d.git \
    cd mmdetection3d \
    pip3 install -v -e . \
    pip3 install cumm-cu113 \
    pip3 install spconv-cu113\
    pip install nuscenes-devkit

RUN git clone https://github.com/r04522611/KP-FUTR3D.git


# use the default bashrc provided by Ubuntu & change the color to yellow
RUN cp /etc/skel/.bashrc /etc/bash.bashrc && \
    sed -i 's/#force_color_prompt/force_color_prompt/g' /etc/bash.bashrc && \
    sed -i 's/32m/33m/g' /etc/bash.bashrc && \
    echo "\nalias pylab='ipython --pylab'" >> /etc/bash.bashrc

# make python modules in /root/.local visible to non-root users
RUN find /root -type d -exec chmod 755 {} +

RUN rm -rf /tmp/* /var/lib/apt/lists/*
