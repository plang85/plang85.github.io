---
layout: post
title:  "LSF GPU queue for Docker runs"
date:   2017-11-28
categories: hpc neural-networks docker
---

Short notes :construction: on an experimental set-up, Centos 7 based host in an LSF queue that accepts runs for NVIDIA's Cuda enabled Docker image.

### Docker 
[See this](https://docs.docker.com/engine/installation/linux/docker-ce/centos/#set-up-the-repository)
```
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2

sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install docker-ce
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
```
make sure to logout.

### Nvidia
[See this](http://docs.nvidia.com/cuda/cuda-quick-start-guide/index.html)
```
lspci | grep -i nvidia
uname -r
sudo yum install kernel-devel-3.10.0_693.el7.x86_64 kernel-headers-3.10.0_693.el7.x86_64
sudo yum install http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/cuda-repo-rhel7-8.0.61-1.x86_64.rpm
sudo yum install cuda-8-0
sudo /sbin/shutdown -r now
export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
$ export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64\
 ${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```
[and this](https://github.com/NVIDIA/nvidia-docker)
```
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
  sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/ubuntu16.04/amd64/nvidia-docker.list | \
  sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update

# Install nvidia-docker2 and reload the Docker daemon configuration
sudo apt-get install -y nvidia-docker2
sudo pkill -SIGHUP dockerd
```

### Submit
[See this](https://hub.docker.com/r/philipplang/deeplearning/)
```
#!/bin/bash
#
# LSF batch script to run Docker-Cuda neural network example
#
#BSUB -J mnist_example
#BSUB -o mnist_example.out
#BSUB -e mnist_example.err
#BSUB -q "gpu02"
#BSUB -m "abg-gpu02"

docker run --runtime=nvidia --rm --mount type=bind,source=/xxx/yyy,target=/var/run philipplang/deeplearning /bin/bash -c "cd /var/run; python main.py"
```
