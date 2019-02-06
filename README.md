## Mount system.img

```
sudo apt install android-tools-fsutils
simg2img system.img system.img.raw
sudo mount -t ext4 -o loop system.img.raw /mnt/
```

ref: http://omappedia.org/wiki/Android_eMMC_Booting#Modifying_.IMG_Files

## Deodex framework odex

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
