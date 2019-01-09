## Installing the NVIDIA CUDA toolkit on Ubuntu 18.04

### Check if the new NVIDIA GPU really exists

```
lspci | grep -i nvidia
```

### Install Linux headers
```
sudo apt-get install linux-headers-$(uname -r)
```

### Download and install the cuda driver .deb package
https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal

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

# NVIDIA CUDA Tools PATH
export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}

EOF

source .bashrc
```

### Verify the CUDA installation

```
cat /proc/driver/nvidia/version
nvcc --version
nvidia-smi
```

A CUDA compatible version of the NVIDIA driver is installed as part of the CUDA Toolkit installation. 
By adding the Ubuntu nvidia-drivers ppa, apt takes care of updating nvidia-drivers, hoping that the 
drivers version is compatible to the CUDA version. The compatibility matrix is in Table 1 at
https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html.

```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
sudo apt upgrade
```

To freeze the nvidia-driver to a particulat version, so

```
sudo apt-mark hold nvidia-graphics-drivers-<version>
```

### Reboot and check the nvidia-drivers version

```
nvidia-smi
```

## Notes
After the installation (of ?) a new service exists:

```
systemctl status nvidia-persistenced
```
See https://download.nvidia.com/XFree86/Linux-x86/384.59/README/nvidia-persistenced.html

## References
https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html
https://developer.nvidia.com/cuda-downloads
https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html

## Performance of KDE-Neon vs. Gnome3

Because the machine is also used as a Minecraft and Steam gaming platform, here are some notes about the graphics 
performance with nvidia-drivers 410.78 (Dec 2018) and NVIDIA GEFORCE RTX 2080.

1. KDE-Neon (kwin)

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
nearly 100% utilization. The game lags; same for Raft/Steam. There are also mouse pointer teardropping effects.

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

### Results
The CPU utilization decreased a bit but still too high.
The latest beta driver also improved things. There’s been some work done on kwin and the entire kde framework 
to make it more efficient.
See this thread: https://forum.manjaro.org/t/plasma-kwin-nvidia-faster-huh/65547
Reported with nvidia 410.73.

2. Gnome3

The gaming experience is almost perfect. Minecraft performs around 1000fps. 
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
