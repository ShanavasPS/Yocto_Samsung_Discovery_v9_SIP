## **Install the required packages**

```sh
sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib \
build-essential chrpath socat libsdl1.2-dev xterm
```


Download the BSP file from the share point using this link Discovery V9 RC5 LV1_Software_Baremetal Linux BSP images_ALL_REV 1.01.gz



Extract the downloaded .gz file using the below commands
```sh
tar -xvf Discovery\ V9\ RC5\ LV1_Software_Baremetal\ Linux\ BSP\ images_ALL_REV\ 1.01.gz
```
It won’t show up in auto-complete if you type ‘tab’ as the extension is ‘.gz’

After the extraction succeeds, you can see the below files inside it
```sh
cd Discovery-RC5-LV1-bare-Linux-tarball/
ls -l
build_bare.sh
README.md
sources
yocto_fetch.sh
```


## **Opensource Download**

Before building, you need to run yocto_fetch.sh first.
This is a file to download Opensources(poky, meta-openembedded, and meta-clang)
required for Discovery build.
```sh
source yocto_fetch.sh
```


## **Prepare the build**

Then run buld_bare.sh to stat the build.
Linux Baremetal is based on yocto build.
Select number [3] v9-discovery and number [2] Linux Baremetal.

```sh
source build_bare.sh 3 2
```


## **Add OpenCV support**

Add the below lines of code to the end of build_bare/conf/local.conf.
This adds a specific version of OpenCV, 4.1.0, to the build
```sh
DISTRO_FEATURES_append = " opencv-sdk"
PREFERRED_VERSION_opencv = "4.1.0%"

CORE_IMAGE_EXTRA_INSTALL += "libopencv-core-dev libopencv-highgui-dev libopencv-imgproc-dev libopencv-objdetect-dev libopencv-ml-dev"
CORE_IMAGE_EXTRA_INSTALL += "opencv-apps opencv-dev"
```


## **Add GTK support to OpenCV**

The Yocto won't include the support for GTK and OpenCL by default. There is a way to build OpenCV using Yocto by adding:
```sh
PACKAGECONFIG_append_pn-opencv = " gtk opencl"
```
to the end of build_bare/conf/local.conf file

The OpenCV recipe has to be modified as well, in sources/meta-openembedded/meta-oe/recipes-support/opencv/opencv_4.1.0.bb, replace:
```sh
PACKAGECONFIG[opencl] = "-DWITH_OPENCL=ON,-DWITH_OPENCL=OFF,opencl-headers virtual/opencl-icd,"
```
with:
```sh
PACKAGECONFIG[opencl] = "-DWITH_OPENCL=ON,-DWITH_OPENCL=OFF,"
```


## **Add rpm package**

Add the below line of code to the end of build_bare/conf/local.conf to add the rpm package to the image.
```sh
EXTRA_IMAGE_FEATURES += "package-management"
```

## **Add other necessary packages**


The local.conf file would look like as shown below at the end
```sh
require conf/euto-v9-common/local.conf

MACHINE ??= "euto-v9-discovery"

DISTRO_FEATURES_append = " opencv-sdk wayland vulkan"
PREFERRED_VERSION_opencv = "4.1.0%"

CORE_IMAGE_EXTRA_INSTALL += "libopencv-core-dev libopencv-highgui-dev libopencv-imgproc-dev libopencv-objdetect-dev libopencv-ml-dev libopencv-video libopencv-videoio"
CORE_IMAGE_EXTRA_INSTALL += "opencv-apps opencv-dev"
IMAGE_INSTALL_append = " rsync unzip vulkan-loader vulkan-tools binutils openssh-sftp-server"

PACKAGECONFIG_append_pn-opencv = " gtk opencl"

EXTRA_IMAGE_FEATURES += "package-management tools-debug"

IMAGE_ROOTFS_EXTRA_SPACE = "4100200"
```


## **Start the build**

Execute the following command to run the build.

```sh
bitbake core-image-bare
```
Build images are placed under the following path,
```sh
$HOME/build_bare/tmp/deploy/images/euto-v9-discovery
```


All done!!!.

Refer to the document Discovery V9 RC5 LV1_User Manual_Baremetal Linux BSP document_ALL_REV 5.01.pdf on flashing instructions.
