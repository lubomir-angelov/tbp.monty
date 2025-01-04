# Local setup 
The following additional steps had to be done to run the tests locally:

- disable the parallelism of pytest (comment out addopts in pyproject toml)
```bash
[tool.pytest.ini_options]
minversion = '6.0'
# disable parallelism of tests to avoid memory errros and failed tests
# addopts = '-ra -n auto'
```

- install additional packages 
```bash
# install
$ sudo apt-get install -y     libgl1-mesa-glx     libgl1-mesa-dri     libglu-dev     libosmesa6     libosmesa6-dev     freeglut3-dev     mesa-utils     xvfb

# reset configs
$ sudo ldconfig

# check swrast dri
$ dpkg -L libgl1-mesa-dri | grep swrast_dri.so
/usr/lib/x86_64-linux-gnu/dri/kms_swrast_dri.so
/usr/lib/x86_64-linux-gnu/dri/swrast_dri.so

# check nvidia driver and install nvidia healdess closest to driver version
$ nvidia-smi
Sat Jan  4 11:49:45 2025
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 560.35.02              Driver Version: 560.94         CUDA Version: 12.6     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA GeForce RTX 3090        On  |   00000000:07:00.0  On |                  N/A |
| 30%   52C    P0            121W /  350W |     667MiB /  24576MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+

+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI        PID   Type   Process name                              GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|    0   N/A  N/A        34      G   /Xwayland                                   N/A      |
+-----------------------------------------------------------------------------------------+

$ apt-cache search nvidia | grep -P '^nvidia-(headless-)?[0-9]+\s'
$ sudo apt-get install nvidia-headless-550

# verify habitat sim
$ python -c "import habitat_sim; print(habitat_sim.__version__)"                                                                                               
0.2.2      

# simlink the swrat.so file
$ sudo mkdir /usr/lib/dri
$ sudo ln -s /usr/lib/x86_64-linux-gnu/dri/swrast_dri.so /usr/lib/dri/swrast_dri.so

# run the tests consecutively without xdist
pytest -p no:xdist tests/unit -v -s -rP
...
=============================================================================== 318 passed, 486 warnings in 757.51s (0:12:37) ===============================================================================
```