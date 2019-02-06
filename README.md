# Introduction

This repository contains my note and collected tools to reverse engineer and understand the Samsung Android camera stack.

Environment: Ubuntu 16.04

# SamsungCamera app

To obtain the APK, we need to extract it from the system image.

## Mount system.img

Of course, you need to obtain your `system.img` from Samsung stock ROM. You can extract it from your rooted device or simply download from sites like [Sammobile](https://www.sammobile.com/FIRMWARES/).

```
sudo apt install android-tools-fsutils
simg2img system.img system.img.raw
sudo mount -t ext4 -o loop system.img.raw /mnt/
```

ref: http://omappedia.org/wiki/Android_eMMC_Booting#Modifying_.IMG_Files

The SamsungCamera app is located in `/mnt/priv-app/SamsungCamera7/SamsungCamera7.apk`, copy it somewhere and use standard APK analysis tool to analyze. A good all-in-one tool is the [Mobile Security Framework](https://github.com/MobSF/Mobile-Security-Framework-MobSF). I will not detail the steps here, since it's already covered by many other tutorials.

## Analysis

Main app should be this one: `com/sec/android/app/camera/Camera.java`, it delegates the camera operation to the "Engine".

Here's the Engine interface `com/sec/android/app/camera/interfaces/Engine.java`, camera is opened by the implementation of the Engine, called "CommonEngine" here in `com/sec/android/app/camera/engine/CommonEngine.java`:

```
1547     private void openCameraDevice() {
```

`mCameraDevice` is of type `SemCamera`, i.e. `com.samsung.android.camera.core.SemCamera`, which is implemented in the system framework. So to understand camera operations, I need to look deeper into SemCamera.

But turned out getting the Java source code of SemCamera is not that easy..

## Deodex framework odex

`/mnt/framework/semcamera.jar` is just empty, a quick search of files named `"semcamera*"` returned `/mnt/framework/oat/arm/semcamera.odex`, looking at the file size, there should be something inside.

Deodex:

```
java -jar baksmali-2.2.6.jar x /mnt/framework/oat/arm/semcamera.odex -d /mnt/framework/arm/
```

Deodexed smali files will be put in `out/`

ref:
- https://www.jianshu.com/p/a1f4ffc0254e
- https://github.com/JesusFreke/smali/wiki/DeodexInstructions

Use `smali` to assemble dex:

```
java -jar smali-2.2.6.jar out -o classes.dex
```

Use `jadx` to put back Java source:

```
bin/jadx -d java_out out.dex
```

Result Java source will be put in `java_out`

## References

- Related work: https://hackcorrelation.blogspot.com/2017/08/a-look-inside-samsung-s7-camera.html
- https://dorylabs.com/enable-1440p-video-recording-60fps-on-galaxy-s8/

Other ports I have found:

- https://forum.xda-developers.com/galaxy-s8/themes/port-samsung-s9-camera-port-ar-emoji-t3826284
- https://forum.xda-developers.com/note5/themes-apps/s8-camera-port-t3592871/page3

To understand camera app operations, take a look at [OpenCamera source code](https://github.com/almalence/OpenCamera/blob/master/src/com/almalence/opencam/cameracontroller/CameraController.java)
