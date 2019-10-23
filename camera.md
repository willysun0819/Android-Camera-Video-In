# The quick notes of android camera
self pen notes regarding camera module porting on android 9 rk3368, imx6, imx8 SoC platform.



## Command
```
// list /dev/video*
grep -H '' /sys/class/video4linux/video*/name
adb shell cat /sys/class/video4linux/*/name

// show media framework topology graph of /dev/media*
media-ctl -p /dev/media0

// set format and dump frames
v4l2-ctl -d /dev/video0 \
    --set-fmt-video=width=720,height=480,pixelformat=NV12 \
    --stream-mmap=3 \
    --stream-skip=3 \
    --stream-to=/sdcard/frame.out \
    --stream-count=1 \
    --stream-poll

// dump native serivce of media.camera
adb shell dumpsys media.camera

// quick launch camera app
am start -n com.android.camera2/com.android.camera.CameraActivity
```
> ref: [media-ctl](./utils/media-ctl), [v4l2-ctl](./utils/v4l2-ctl) are the binary executable files built on android 9 platform.


</br>

## Process

```
    <Java> Camera App : com.android.camera2

    <Java> media.camera

    <native> cameraserver : cameraserver

    <native> hidl_service : android.hardware.camera.provider@2.4-service


user space

---------------------------------------------------------

kernel space

    camera sensor driver + V4l2 framework +  Media Controller framework
```

> Ref: [Rockchip-isp1 - Rockchip open source Document](http://opensource.rock-chips.com/wiki_Rockchip-isp1)



</br>

## Source code

- camera API : [frameworks/base/core/java/android/hardware](http://androidxref.com/9.0.0_r3/xref/frameworks/base/core/java/android/hardware/)
    - [Camera.java](http://androidxref.com/9.0.0_r3/xref/frameworks/base/core/java/android/hardware/Camera.java)
    - [CameraInfo.java](http://androidxref.com/9.0.0_r3/xref/frameworks/base/core/java/android/hardware/CameraInfo.java)
    - [CameraStatus.java](http://androidxref.com/9.0.0_r3/xref/frameworks/base/core/java/android/hardware/CameraStatus.java)
    - [camera2/](http://androidxref.com/9.0.0_r3/xref/frameworks/base/core/java/android/hardware/camera2/)
- cameraserver :
    - client : [frameworks/av/camera](http://androidxref.com/9.0.0_r3/xref/frameworks/av/camera/)
    - server : [frameworks/av/services/camera](http://androidxref.com/9.0.0_r3/xref/frameworks/av/services/camera/)
- camera metedata : [system/media/camera](http://androidxref.com/9.0.0_r3/xref/system/media/camera/)
    - [camera_metadata.h](http://androidxref.com/9.0.0_r3/xref/system/media/camera/include/system/camera_metadata.h)
    - [camera_metadata_tags.h](http://androidxref.com/9.0.0_r3/xref/system/media/camera/include/system/camera_metadata_tags.h)
    - [camera_vendor_tags.h](http://androidxref.com/9.0.0_r3/xref/system/media/camera/include/system/camera_vendor_tags.h)
- camera HIDL : [hardware/interfaces/camera](http://androidxref.com/9.0.0_r3/xref/hardware/interfaces/camera/)
    - [common](http://androidxref.com/9.0.0_r3/xref/hardware/interfaces/camera/common/)
    - [device](http://androidxref.com/9.0.0_r3/xref/hardware/interfaces/camera/device/)
    - [metadata](http://androidxref.com/9.0.0_r3/xref/hardware/interfaces/camera/metadata/)
    - [provider](http://androidxref.com/9.0.0_r3/xref/hardware/interfaces/camera/provider/)
- legacy camera HAL:
    - definition: [hardware/libhardware/include/hardware]()
        - [camera.h](http://androidxref.com/9.0.0_r3/xref/hardware/libhardware/include/hardware/camera.h) : HAL 1
        - [camera2.h](http://androidxref.com/9.0.0_r3/xref/hardware/libhardware/include/hardware/camera2.h) : HAL 2
        - [camera3.h](http://androidxref.com/9.0.0_r3/xref/hardware/libhardware/include/hardware/camera3.h) : HAL 3
        - [camera_common.h](http://androidxref.com/9.0.0_r3/xref/hardware/libhardware/include/hardware/camera_common.h)
    - implement: [hardware/libhardware/modules/camera/]( http://androidxref.com/9.0.0_r3/xref/hardware/libhardware/modules/camera/)
        - [3.0](http://androidxref.com/9.0.0_r3/xref/hardware/libhardware/modules/camera/3_0/)
        - [3.4](http://androidxref.com/9.0.0_r3/xref/hardware/libhardware/modules/camera/3_4/)
- vendor camera HAL :
    - imx6/imx8 : vendor/nxp-opensource/imx/libcamera3
    - rk3368 :
        - hardware\rockchip\camera :  rk camera HAL
        - hardware\rockchip\camera_engine_rkisp :  rk 3A engine

- kernel : (take ov5640 for example)
  - imx6 : vendor/nxp-opensource/kernel_imx/drivers/media/platform/mxc/capture/ov5640_mipi_v2.c
  - imx8 : vendor/nxp-opensource/kernel_imx/drivers/media/platform/imx8/ov5640_mipi_v3.c
  - rk3368 :
    - kernel/drivers/media/platform/rockchip/isp1 :  rkisp1
    - kernel/drivers/media/i2c/ov5640.c





</br>

## Miscellaneous

##### enable ALOGD, ALOGV
```
// define it in the top line of c/c++ source fiie
#define LOG_NDEBUG 0

// append it in CFALGS of makefile
LOCAL_CFLAGS +=: -DNDEBUG=0		// but seems not work?
```

> ref:  [ANDROID中C++层打开ALOGV打印的LOG开关](https://blog.csdn.net/yu741677868yu/article/details/80682182)
