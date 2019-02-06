## Deodex framework odex

Deodex:

```
java -jar baksmali-2.2.6.jar x /mnt/framework/oat/arm/semcamera.odex -d /mnt/framework/arm/
```

Deodexed smali files will be put in `out/`

ref:
- https://www.jianshu.com/p/a1f4ffc0254e

Use `smali` to assemble dex:

```
java -jar smali-2.2.6.jar out -o classes.dex
```

Use `jadx` to put back Java source:

```
bin/jadx -d java_out out.dex
```

Result Java source will be put in `java_out`
