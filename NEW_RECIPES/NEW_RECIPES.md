# HƯỚNG DẪN BUILD LAYER 


### Xem các layer được build 

```
cat bbb/build/conf/bblayers.conf
```

Thấy rằng nó có các layer như sau 

/home/asus/yocto/poky-kirkstone/meta \

/home/asus/yocto/poky-kirkstone/meta-poky \

/home/asus/yocto/poky-kirkstone/meta-yocto-bsp \

Chúng ta sẽ thêm 1 recipe mới trong một meta bất kì ở đây chọn meta-poky


```
cd /home/asus/yocto/poky-kirkstone/meta-poky
mkdir -p recipes-mytest
```

Tạo một folder chứa các package recipes 

Sau đó triển khai hello như sau , 1 file source và 1 file recipe để hướng dẫn bitbake biên dịch và install
```
cd recipes-mytest
mkdir -p hello
touch hello/hello.bb
mkdir -p hello/files 
touch hello/files/helloworld.c
```
Kiểm tra thử cấu trúc của nó sau khi tạo thêm 
```
tree hello
```
Triển khai helloword.c
```
#include <stdio.h>

int main(int argc, char *argv[])
{
    printf("Hello world!\n");

    return 0;
}
```
Triển khai file recipes

hello.bb
```
SUMMARY = "Simple helloworld application"
# Mô tả về file recipe
SECTION = "examples"
# Phân loại recipe , không có cũng được
LICENSE = "MIT"
# Giấy phép 
LIC_FILES_CHKSUM = "file://${COMMON_LICENSE_DIR}/MIT;md5=0835ade698e0bcf8506ecda2f7b4f302"
# Giấy phép kiểm tra xem có thay đổi hay không
# Cú pháp chung 

# Nơi lấy dữ liệu
SRC_URI = "file://helloworld.c"


# Biến toàn cục của bitbake 
S = "${WORKDIR}"
# Sử dụng để xác định vị trí thư mục làm việc 
# Sau đó bitbake sẽ gọi nó


# Biên dịch helloworld 
do_compile() {
    ${CC} ${LDFLAGS} helloworld.c -o helloworld
}

# install 
do_install() {
    install -d ${D}${bindir}
    install -m 0755 helloworld ${D}${bindir}
}
```

### Tham khảo thêm một số biến toàn cục khác tại đây 
```
https://docs.yoctoproject.org/ref-manual/variables.html
```

### Kiểm tra xemm đã có hello chưa
```
bitbake -s | grep hello
```
### Biên dịch recipe hello 
```
bitbake hello
```
### Check thử hello trong yocto 
```
find -name hello
```