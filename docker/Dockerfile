# Use an official Miniconda image as a parent image
FROM continuumio/miniconda3

# Set the working directory in the container
WORKDIR /usr/src/app

# Copy the current directory contents into the container at /usr/src/app
# COPY . .


# Create the conda environment
RUN conda create -n openins3d python=3.9 -y
SHELL ["conda", "run", "-n", "openins3d", "/bin/bash", "-c"]

# Install the required packages
RUN conda install pytorch==1.13.1 torchvision==0.14.1 torchaudio==0.13.1 pytorch-cuda=11.6 -c pytorch -c nvidia -y
# ENV PATH /opt/conda/bin:/usr/local/bin:${PATH}
ENV NVIDIA_VISIBLE_DEVICES all
RUN nvidia-smi

RUN conda install lightning -c conda-forge -y
RUN conda install -c "nvidia/label/cuda-11.6.1" libcusolver-dev -y
RUN conda install pytorch3d -c pytorch3d -y

# Install gcc and g++ and verify their versions
RUN apt-get update && apt-get install -y gcc g++ libopenblas-dev ffmpeg libsm6 libxext6
RUN python -m pip install 'git+https://github.com/facebookresearch/detectron2.git'
RUN conda install nltk -y

# Install additional Python packages
RUN pip install torch_scatter gdown loguru open3d plyfile pyviz3d python-dotenv omegaconf==2.1.1 iopath==0.1.8

# Setup 3rd party lib
RUN git clone "https://github.com/Pointcept/OpenIns3D.git"
WORKDIR /usr/src/app/OpenIns3D/third_party/pointnet2
# ENV PATH /opt/conda/envs/openins3d:$PATH
RUN python setup.py install
WORKDIR /usr/src/app/OpenIns3D/third_party

# Install MinkowskiEngine
RUN git clone --recursive "https://github.com/NVIDIA/MinkowskiEngine"
WORKDIR /usr/src/app/OpenIns3D/third_party/MinkowskiEngine
RUN git checkout 02fc608bea4c0549b0a7b00ca1bf15dee4a0b228
RUN python setup.py install --force_cuda --blas=openblas
WORKDIR /usr/src/app/OpenIns3D

# Install ODISE
RUN git clone https://github.com/NVlabs/ODISE.git
WORKDIR /usr/src/app/OpenIns3D/ODISE
RUN pip install -e .
WORKDIR /usr/src/app/OpenIns3D

# Define environment variable
ENV NAME openins3d
CMD ["conda", "run", "-n", "openins3d"]