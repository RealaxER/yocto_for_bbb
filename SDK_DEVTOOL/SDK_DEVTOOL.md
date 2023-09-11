1) Build SDK 
2) Tìm hiểu devtool
3) Tạo 1 file .bb để gọi tất cả các file .bb mình cần dùng.Sau đó đưa .bb đó vào vào image.



# 1)What is SDK, how to build it ?

+ SDK là bộ công cụ toolchain cung cấp cross-compiler , quemu  dev-tool gdb... cho application , nó giúp một dev enginner khác không cần bộ Yocto vẫn có thể build cho board thông qua các toolchain mà SDK cung cấp 

+ Để build được SDK chúng ta cần nắm rõ SDK gồm : STD SDK và SDK Extensible 
+ Về cơ bản SDK Ext bao gồm SDK STD và còn có khả năng tùy chỉnh thêm hay thêm các toolchain khác vào do tự thêm
+ Còn STD SDK thì không có khả năng đó , nó chỉ build xong và sử dụng.

# Build SDK
Để install SDK STD ta cần thông qua câu lệnh bitbake

+ bitbake -c populate_sdk <name_image> : SDK STD

+ bitbake -c populate_sdk_ext <name_image> : SDK EXT

```
bitbake -c populate_sdk core-image-minimal
```
### Check các tool đã được download
```
ls build/downloads/
```
### Sau khi compile toolchain sẽ được đặt ở trong mục sau
```
ls build/tmp/deploy/sdk/
```

### Cài đặt SDK như sau 
```
cd build
mkdir -p mytest/sdk
./tmp/deploy/sdk/poky-glibc-x86_64-core-image-sato-cortexa8hf-neon-beaglebone-yocto-toolchain-4.0.12.sh
```
Sau đó điền đường dẫn tuyệt đối của thư mục mytest/sdk 

### Check thử cài đặt 
Lúc này sẽ thấy có folder như systemroot , các file tool
```
ls mytest/sdk
```
### Ví dụ cách dùng SDK 
```
cd mytest/sdk
source environment-setup-cortexa8hf-neon-poky-linux-gnueabi
```
### Check thử GCC của SDK 
```
$CC -v 
```
Thử biên dịch với hello.c
```
$CC -o hello hello.c
```
Tuy nhiên đây là cross_compiler nên hello không chạy được trên môi trường này.
```
file hello
```

# 2) DEVTOOL 
### OVERVIEW 
+ Devtool không có trên SDK Standard nó chỉ có ở trong SDK Extensible

+ devtool hoàn toàn có thể thay thế cho bitbake trong nhiều trường hợp xử lý recipes và các layers

+ devtool có 3 câu lệnh cơ bản để xử lý recipe như add,modify ,upgrade

+ Để xem cụ thể các câu lệnh của devtool hay gõ devtool --h

### Thêm 1 recipe vào workspace 
+ Trong devtool sẽ có 1 không gian làm việc riêng giúp copy các recipe bản gốc vào workspace đó và handle 

+ Ở devtool coi đó là 1 workspace , còn bản chất nó chính là 1 layer

Thêm 1 module recipe vào workspace 
```
devtool modify hello
```
+ Đảm bảo hello có tồn tại trong các layer khác , sau đó devtool sẽ tự động tìm 

Nếu muốn thêm file recipe không thôi 
```
devtool add meta-mylayer/hello
```
+ Nó sẽ tìm trong module hello để tìm và thêm các file .bb 


### a) Tự tạo một recipe và layer 
Để tạo một layer riêng ta cần dùng câu lệnh sau 
```
devtool create-workspace meta-test
```
Check thử xem đã được thêm vào bblayer chưa 
```
bitbake-layers show-layers
```
devtool có khả năng lấy các module có sẵn để tạo module vào layer mới sau.

+ Cơ chế tải trên mạng http ,git, (fetch)
+ Tải từ source của một layer khác.
```
devtool add meta-mylayer/hello/hello.bb
```
Câu lệnh add chỉ có thể thêm các file .bb vào hệ thống 

Để thêm cả module vào ta cần dùng modify
```
devtool modify hello
```

+ Cả hai câu lệnh sẽ gọi tạo ra các tệp .bb và .bbappend mới sau đó gọi lại recipe cũ để ghi thêm 

### b) Chỉnh sửa recipes có sẵn bằng devtool 
Sau khi thêm vào workspace bạn có thể chỉnh sửa nó bằng câu lệnh sau.

+ Nếu nó chưa được thêm vào recipe thì nó sẽ chỉnh sửa ở layer của nó.
+ Khi thêm vào workspace thì nó sẽ ưu tiên sửa trong workspace
```
devtool edit-recipe hellocmake
```

### c) Build recipe không thôi 
```
devtool build hello
```
Build image vơi devtool 
```
devtool build-image <image-name>
```
### d) Thêm patch 
Sau khi thay đổi ta sẽ lựa chọn 1 chỗ để lưu những bản patch đó bằng câu lệnh finish
```
devtool finish <recipe> <destination>
```
+ recipe : cần lưu 
+ destination : điểm đến , nơi lưu
```
devtool finish hello meta-mylayer 
```

# 3) Sử dụng 1 file .bb để quản lý các file .bb khác 

Dựa vào meta-mylayer ta xây dựng lại 
 
```
cd meta-mylayer
mkdir -p recipes-core/packagegroup-bbb/
code recipes-core/packagegroup-bbb/packagegroup-bbb.bb
```
packagegroup-bbb.bb
```
DESCRIPTION = "Simple packagegroup"
LICENSE = "MIT"
SECTION = "mylayer"
PR = "r0"

inherit packagegroup
RDEPENDS:${PN} = "example"
```
Sửa lại core-image-minimal.bb hoặc thêm vào local.conf
```
IMAGE_INSTALL:append = " helloauto"
IMAGE_INSTALL:append = " packagegroup-bbb"
```
