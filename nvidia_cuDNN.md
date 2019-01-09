## Installation of cuDNN on Ubuntu 18.04

The NVIDIA CUDA® Deep Neural Network library (cuDNN) is a GPU-accelerated library of primitives for deep neural networks.

Determine the compatible cuDNN version to install at https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html#install-linux.

**I am going to install cuDNN v7.4.2 (Dec 14, 2018), for CUDA 10.0.**

### Downloading cuDNN Libs

This requires to join the NVIDIA Developer Program. 
I am going to install the cuDNN libs from the provided .tgz file.
The runtime .deb for Ubuntu 18.04 returned me an error when running the test case (`-l cudnn: file not found`).

### Installing cuDNN from the Tar File

```
cd Downloads/
tar -xzvf cudnn-10.0-linux-x64-v7.4.2.24.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

### Verifying cuDNN

This works only if the .deb was installed earlier because the .tgz does not contain cudnn_samples_v7/.

```
cp -r /usr/src/cudnn_samples_v7/ $HOME
cd $HOME/cudnn_samples_v7/mnistCUDNN
make clean && make
./mnistCUDNN
==> Test passed!
```

### Issues

There is a `ldconfig` error for cuDNN when running `apt update / upgrade`.

```
$ sudo ldconfig
/sbin/ldconfig.real: /usr/local/cuda-10.0/targets/x86_64-linux/lib/libcudnn.so.7 ist kein symbolischer Link
```

This is because libcudnn.so and libcudnn.so.5 are not symlinks.

```
/usr/local/cuda/lib64$ ls -lha libcudnn*
-rwxr-xr-x 1 root root 333M Dez 19 08:59 libcudnn.so
-rwxr-xr-x 1 root root 333M Dez 19 08:59 libcudnn.so.7
-rwxr-xr-x 1 root root 333M Dez 19 08:59 libcudnn.so.7.4.2
-rw-r--r-- 1 root root 331M Dez 19 08:59 libcudnn_static.a
```

To fix this, do:

```
/usr/local/cuda/lib64$ sudo rm libcudnn.so
/usr/local/cuda/lib64$ sudo rm libcudnn.so.5
/usr/local/cuda/lib64$ sudo ln libcudnn.so.5.1.5 libcudnn.so.5
/usr/local/cuda/lib64$ sudo ln libcudnn.so.5 libcudnn.so
```

Run `sudo ldconfig` again and there should be no errors.
