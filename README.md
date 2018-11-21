# cuda9.0-install-opencv2.4.13.6
安装依赖包：

sudo apt-get install build-essential
sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
sudo apt-get install cmake git libgtk2.0-dev pkg-config

安装解码音视频库：

sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev

修改opencv编译的脚本是因为两点:

1.在cuda9里面，NVIDIA把 libnppi.so换成libnppc.so libnppial.so libnppicc.so libnppicom.so libnppidei.so libnppif.so libnppig.so libnppim.so libnppist.so libnppisu.so libnppitc.so libnpps.so

2.cuda9里面不支持‘ compute-20 ’

1.解决第一个问题： 
修改opencv/cmake/FindCUDA.cmake 文件，将524行的

unset(CUDA_nppi_LIBRARY CACHE)

修改为：

unset(CUDA_nppial_LIBRARY CACHE)
unset(CUDA_nppicc_LIBRARY CACHE)
unset(CUDA_nppicom_LIBRARY CACHE)
unset(CUDA_nppidei_LIBRARY CACHE)
unset(CUDA_nppif_LIBRARY CACHE)
unset(CUDA_nppig_LIBRARY CACHE)
unset(CUDA_nppim_LIBRARY CACHE)
unset(CUDA_nppist_LIBRARY CACHE)
unset(CUDA_nppisu_LIBRARY CACHE)
unset(CUDA_nppitc_LIBRARY CACHE)

修改opencv/cmake/FindCUDA.cmake 文件，将799行和801行的：

find_cuda_helper_libs(nppi)
set(CUDA_npp_LIBRARY"${CUDA_nppc_LIBRARY};${CUDA_nppi_LIBRARY};${CUDA_npps_LIBRARY}")

find_cuda_helper_libs(nppial)
find_cuda_helper_libs(nppicc)
find_cuda_helper_libs(nppicom)
find_cuda_helper_libs(nppidei)
find_cuda_helper_libs(nppif)
find_cuda_helper_libs(nppig)
find_cuda_helper_libs(nppim)
find_cuda_helper_libs(nppist)
find_cuda_helper_libs(nppisu)
find_cuda_helper_libs(nppitc)
set(CUDA_npp_LIBRARY "${CUDA_nppc_LIBRARY};${CUDA_nppial_LIBRARY};${CUDA_nppicc_LIBRARY};${CUDA_nppicom_LIBRARY};${CUDA_nppidei_LIBRARY};${CUDA_nppif_LIBRARY};${CUDA_nppig_LIBRARY};${CUDA_nppim_LIBRARY};${CUDA_nppist_LIBRARY};${CUDA_nppisu_LIBRARY};${CUDA_nppitc_LIBRARY};${CUDA_npps_LIBRARY}")

2.解决第二个问题方案 
更改 OpenCVDetectCUDA.cmake 文件，把有关 ‘ compute-20 ’ 的修改掉。 
将74行中的：

73    if(CUDA_GENERATION STREQUAL "Fermi")
74     set(__cuda_arch_bin "2.0 2.1(2.0)")
75     elseif(CUDA_GENERATION STREQUAL "Kepler")
76     if(${CUDA_VERSION} VERSION_LESS "5.0")
77       set(__cuda_arch_bin "3.0")
78     else()
79       set(__cuda_arch_bin "3.0 3.5")
80    endif()

修改成：

73    if(CUDA_GENERATION STREQUAL "Fermi")
74     set(__cuda_arch_bin "3.0 3.5")
75     elseif(CUDA_GENERATION STREQUAL "Kepler")
76     if(${CUDA_VERSION} VERSION_LESS "5.0")
77       set(__cuda_arch_bin "3.0")
78     else()
79       set(__cuda_arch_bin "3.0 3.5")
80    endif()

将107行：

104      if(${CUDA_VERSION} VERSION_LESS "5.0")
105         set(__cuda_arch_bin "1.1 1.2 1.3 2.0 2.1(2.0) 3.0")
106       elseif(${CUDA_VERSION} VERSION_GREATER "6.5")
107         set(__cuda_arch_bin "2.0 2.1(2.0) 3.0 3.5")
108       else()
109         set(__cuda_arch_bin "1.1 1.2 1.3 2.0 2.1(2.0) 3.0 3.5")
110       endif()
111       set(__cuda_arch_ptx "3.0")
112     endif()

修改成：

104      if(${CUDA_VERSION} VERSION_LESS "5.0")
105         set(__cuda_arch_bin "1.1 1.2 1.3 2.0 2.1(2.0) 3.0")
106       elseif(${CUDA_VERSION} VERSION_GREATER "6.5")
107         set(__cuda_arch_bin "3.0 3.5")
108       else()
109         set(__cuda_arch_bin "1.1 1.2 1.3 2.0 2.1(2.0) 3.0 3.5")
110       endif()
111       set(__cuda_arch_ptx "3.0")
112     endif()

编译安装：

mkdir release
cd release
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..
make -j7 #此处看您的cpu内核数，本人机器8核
sudo make install

