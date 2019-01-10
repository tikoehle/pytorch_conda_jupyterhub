## Installing the NVIDIA CUDA toolkit on Ubuntu 18.04
This is directly taken from the NVIDIA installation guide at [[2]](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html). The following describes the essential steps for Ubuntu.

### Check if the new NVIDIA GPU really exists

```
lspci | grep -i nvidia
```

### Install Linux headers
```
sudo apt-get install linux-headers-$(uname -r)
```

### Download and install the cuda driver .deb package

```
https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal
```

```
cd Downloads/
sudo dpkg -i cuda-repo-ubuntu1804-10-0-local-10.0.130-410.48_1.0-1_amd64.deb 
sudo apt-key add /var/cuda-repo-10-0-local-10.0.130-410.48/7fa2af80.pub
sudo dpkg -i cuda-repo-ubuntu1804-10-0-local-10.0.130-410.48_1.0-1_amd64.deb 
sudo apt update
sudo apt install cuda
```

### Update the $PATH for nvidia tools

```
tee -a $HOME/.bashrc >/dev/null <<EOF

# NVIDIA CUDA Tools
export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}

# NVIDIA CUDA LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

EOF

source .bashrc
```

A CUDA compatible version of the NVIDIA driver is installed as part of the CUDA Toolkit installation. 
By adding the Ubuntu nvidia-drivers ppa, apt takes care of updating nvidia-drivers, hoping that the 
drivers version is compatible to the CUDA version. The compatibility matrix is in table 1 of [1].
The compatibility matrix of Linux distribution and kernle, gcc, glibc is in table 1 of [2].

```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
sudo apt upgrade
```

To freeze the nvidia-driver to a particular version, do

```
sudo apt-mark hold nvidia-graphics-drivers-<version>
```

### Reboot and check the nvidia-drivers version

```
nvidia-smi
```

### Notes
After the installation of CUDA a new service exists provided by NVIDIA.

```
systemctl status nvidia-persistenced
```
See http://docs.nvidia.com/deploy/driver-persistence/index.html#persistence-daemon

### Verify the CUDA installation
#### Versions
```
$ nvcc --version
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2018 NVIDIA Corporation
Built on Sat_Aug_25_21:08:01_CDT_2018
Cuda compilation tools, release 10.0, V10.0.130

$ cat /proc/driver/nvidia/version
NVRM version: NVIDIA UNIX x86_64 Kernel Module  410.78  Sat Nov 10 22:09:04 CST 2018
GCC version:  gcc version 7.3.0 (Ubuntu 7.3.0-27ubuntu1~18.04) 

$ nvidia-smi
Thu Jan 10 09:44:00 2019       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 410.78       Driver Version: 410.78       CUDA Version: 10.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce RTX 2080    Off  | 00000000:01:00.0  On |                  N/A |
| 41%   31C    P8    15W / 225W |    138MiB /  7949MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|    0      1230      G   /usr/lib/xorg/Xorg                            59MiB |
|    0      1297      G   /usr/bin/gnome-shell                          77MiB |
+-----------------------------------------------------------------------------+
```

#### Running the Binaries
The CUDA .deb includes sample programs in source and statically linked form.
The sources are here: `/usr/local/cuda-10.0/samples/`.
The binaries are here: `/usr/local/cuda-10.0/extras/demo_suite/`.
Running `deviceQuery` shows if everything works correctly.

```
$ ./deviceQuery 
./deviceQuery Starting...

 CUDA Device Query (Runtime API) version (CUDART static linking)

Detected 1 CUDA Capable device(s)

Device 0: "GeForce RTX 2080"
  CUDA Driver Version / Runtime Version          10.0 / 10.0
  CUDA Capability Major/Minor version number:    7.5
  Total amount of global memory:                 7949 MBytes (8335327232 bytes)
  (46) Multiprocessors, ( 64) CUDA Cores/MP:     2944 CUDA Cores
  GPU Max Clock rate:                            1800 MHz (1.80 GHz)
  Memory Clock rate:                             7000 Mhz
  Memory Bus Width:                              256-bit
  L2 Cache Size:                                 4194304 bytes
  Maximum Texture Dimension Size (x,y,z)         1D=(131072), 2D=(131072, 65536), 3D=(16384, 16384, 16384)
  Maximum Layered 1D Texture Size, (num) layers  1D=(32768), 2048 layers
  Maximum Layered 2D Texture Size, (num) layers  2D=(32768, 32768), 2048 layers
  Total amount of constant memory:               65536 bytes
  Total amount of shared memory per block:       49152 bytes
  Total number of registers available per block: 65536
  Warp size:                                     32
  Maximum number of threads per multiprocessor:  1024
  Maximum number of threads per block:           1024
  Max dimension size of a thread block (x,y,z): (1024, 1024, 64)
  Max dimension size of a grid size    (x,y,z): (2147483647, 65535, 65535)
  Maximum memory pitch:                          2147483647 bytes
  Texture alignment:                             512 bytes
  Concurrent copy and kernel execution:          Yes with 3 copy engine(s)
  Run time limit on kernels:                     Yes
  Integrated GPU sharing Host Memory:            No
  Support host page-locked memory mapping:       Yes
  Alignment requirement for Surfaces:            Yes
  Device has ECC support:                        Disabled
  Device supports Unified Addressing (UVA):      Yes
  Device supports Compute Preemption:            Yes
  Supports Cooperative Kernel Launch:            Yes
  Supports MultiDevice Co-op Kernel Launch:      Yes
  Device PCI Domain ID / Bus ID / location ID:   0 / 1 / 0
  Compute Mode:
     < Default (multiple host threads can use ::cudaSetDevice() with device simultaneously) >

deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 10.0, CUDA Runtime Version = 10.0, NumDevs = 1, Device0 = GeForce RTX 2080
Result = PASS
```

## Performance of KDE-Neon vs. Gnome3

Because the machine is also used as a Minecraft and Steam gaming platform, here are some notes about the graphics 
performance with nvidia-drivers 410.78 (Dec 2018) and NVIDIA GEFORCE RTX 2080.

### 1. KDE-Neon (kwin) Plasma on Ubuntu 18.04

```
$ nvidia-smi
Mon Dec 10 12:30:29 2018       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 410.78       Driver Version: 410.78       CUDA Version: 10.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce RTX 2080    Off  | 00000000:01:00.0  On |                  N/A |
| 41%   41C    P0    43W / 225W |    508MiB /  7949MiB |      1%      Default |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|    0      1028      G   /usr/lib/xorg/Xorg                           215MiB |
|    0      1223      G   /usr/bin/kwin_x11                             95MiB |
|    0      1231      G   /usr/bin/krunner                               6MiB |
|    0      1234      G   /usr/bin/plasmashell                         124MiB |
+-----------------------------------------------------------------------------+
```

Generally the Minecraft performance is only around 200fps under kwin (we expected 1000fps) and the 4 CPUs are at 
nearly 100% utilization. The game lags; same for Steam/Raft. There are also mouse pointer teardropping effects.

Here are some knobs to improve the situation but the result did only improve minimal.
- Open nvidia-settings

```
x server display config -> Advanced -> Force Composition Pipeline -> Save to X config file
```

NOTE: Disable the above setting for Gnome3!

- KDE System Settings

```
Display Monitor -> Compositor -> Outputmodul: OpenGL 3.1
```

#### Results
The CPU utilization decreased a bit but still too high.
There’s been some work done on kwin and the entire kde framework to make it more efficient.
See this thread: https://forum.manjaro.org/t/plasma-kwin-nvidia-faster-huh/65547 (reported with nvidia 410.73).

### 2. Gnome3

The gaming experience is almost perfect. Minecraft performs at around 500 .. 1000fps, depending on the mods loaded. 
The CPU utilization is around 30% when actively gaming. 
Steam/Raft performance is now also as expected.

```
$ nvidia-smi
Wed Jan  9 15:26:05 2019       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 410.78       Driver Version: 410.78       CUDA Version: 10.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce RTX 2080    Off  | 00000000:01:00.0  On |                  N/A |
| 41%   42C    P3    40W / 225W |    599MiB /  7949MiB |     32%      Default |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|    0      1186      G   /usr/lib/xorg/Xorg                            18MiB |
|    0      1229      G   /usr/bin/gnome-shell                          57MiB |
|    0      2408      G   /usr/lib/xorg/Xorg                           140MiB |
|    0      2581      G   /usr/bin/gnome-shell                         147MiB |
|    0     12416      G   ...passed-by-fd --v8-snapshot-passed-by-fd    14MiB |
|    0     12464      G   ...b/jvm/java-8-openjdk-amd64/jre/bin/java   211MiB |
|    0     13973      G   ./ts3client_linux_amd64                        7MiB |
+-----------------------------------------------------------------------------+
```

## References
[1] https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html.

[2] https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html

[3] https://developer.nvidia.com/cuda-downloads

[4] https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html
