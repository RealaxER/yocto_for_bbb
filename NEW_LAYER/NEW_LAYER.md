# HƯỚNG DẪN BUILD LAYER 


### CREATE NEW LAYER

```
bitbake-layers create-layer meta-mylayer
```

### ADD LAYER FOR bblayer.conf

```
bitbake-layers add-layer meta-mylayer
```

### Check layer đã được thêm chưa

```
bitbake-layers show-layers
```

### CHECK bblayer.conf

```
cat conf/bblayers.conf
```
### Check layer.conf trong Layer mới 
Sau khi thêm được bblayer bitbake sẽ gọi đến layer.conf của mylayer để kiểm tra các đường dẫn,config, hỗ trợ những recipes gì 

Ở đây có 

${LAYERDIR}/recipes-*/*/*.bb \

${LAYERDIR}/recipes-*/*/*.bbappend"

Thì hỗ trợ .bb và .bbappend 

```
cat meta-mylayer/conf/layer.conf
```

### Tạo recipe trong mylayer 
```
cd meta-mylayer
tree
cd recipes-example/example
```

### Triển khai recipe ở đây 
```
mkdir files
touch files/hello.c
tree
```

hello.c
```
#include <stdio.h>

int main(int argc, char *argv[])
{
    printf("Hello world!\n");

    return 0;
}
```
example_0.1.bb
```
SUMMARY = "bitbake-layers recipe"
LICENSE = "MIT"

python do_display_banner() {
    bb.plain("***********************************************");
    bb.plain("*                                             *");
    bb.plain("*  Example recipe created by bitbake-layers   *");
    bb.plain("*                                             *");
    bb.plain("***********************************************");
}

DESCRIPTION = "Simple hello application"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://${COMMON_LICENSE_DIR}/MIT;md5=0835ade698e0bcf8506ecda2f7b4f302"

SRC_URI = "file://hello.c"

S = "${WORKDIR}"

do_compile() {
        ${CC} hello.c ${LDFLAGS} -o hello
}

do_install() {
        install -d ${D}${bindir}
        install -m 0755 hello ${D}${bindir}
}
```


/yocto/bbb/build$

Build chương trình 
```
bitbake core-image-minimal
```
