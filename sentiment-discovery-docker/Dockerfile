FROM nvidia/cuda:8.0-cudnn7-devel-ubuntu16.04
RUN apt-get update && apt-get install -y --no-install-recommends \
         build-essential cmake git curl vim ca-certificates libjpeg-dev libpng-dev libnccl2 libnccl-dev\
     && rm -rf /var/lib/apt/lists/*
RUN curl -o ~/miniconda.sh -O  https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh  && \
     chmod +x ~/miniconda.sh && \
     ~/miniconda.sh -b -p /opt/conda && \
     rm ~/miniconda.sh && \
     /opt/conda/bin/conda install numpy pyyaml scipy ipython mkl mkl-include && \
     /opt/conda/bin/conda install -c soumith magma-cuda80 && \
     /opt/conda/bin/conda clean -ya
ENV PATH /opt/conda/bin:$PATH
# This must be done before pip so that requirements.txt is available
WORKDIR /opt
RUN git clone https://github.com/pytorch/pytorch.git
WORKDIR /opt/pytorch
RUN git submodule update --init
RUN TORCH_CUDA_ARCH_LIST="3.5 5.2 6.0 6.1" TORCH_NVCC_FLAGS="-Xfatbin -compress-all" \
    CMAKE_PREFIX_PATH="$(dirname $(which conda))/../" \
    pip install -v .
RUN git clone https://github.com/pytorch/vision.git && cd vision && pip install -v .
WORKDIR /workspace
RUN chmod -R a+w /workspace
RUN pip install --upgrade pip
RUN git clone https://github.com/NVIDIA/sentiment-discovery.git
WORKDIR /workspace/sentiment-discovery
# Fix for the name of aten library
RUN sed -i "s|libaten|aten|g" setup.py
RUN python setup.py install
RUN conda install seaborn
