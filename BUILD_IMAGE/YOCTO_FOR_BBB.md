# HƯỚNG DẪN BUILD YOCTO 

### Setup tool và môi trường
```
sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat cpio python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping libsdl1.2-dev xterm bc build-essential chrpath diffstat gawk git libncurses5-dev pkg-config socat subversion texi2html texinfo u-boot-tools lz4
```

### Tải phiên bản kirkstone của Poky Yocto về
```
mkdir -p yocto 
cd yocto
git clone -b kirkstone git://git.yoctoproject.org/poky.git poky-kirkstone
```

### Thiết lập môi trường và chọn đường dẫn để build 

oe-init-build-env là lệnh dùng để thiết lập môi trường.

Nếu không chọn thư mục out nó sẽ mặc định tạo ra một thư mục conf ở pwd.

Sau đó khi biên dịch sẽ biên dịch ở thư mục hiện tại luôn.

Ở đây chọn bbb/build là nơi để build và chứa source.
```
source poky-kirkstone/oe-init-build-env ./bbb/build
```


### Lựa chọn build cho BBB

Tắt MACHINE ??= "qemux86-64" và enable bbb trong 

conf/local.conf
```
MACHINE ?= "beaglebone-yocto"
```


### Biên dịch image 
Có thể thấy rất nhiều image khác sau khi dùng oe-init-build-env như :

core-image-minimal : phiên bản image nhẹ nhất chứa option cơ bản 

core-image-full-cmdline : đầy đủ cmd và interface

core-image-sato : có thêm giao diện đồ họa Sato 

core-image-weston : Đồ họa weston 

meta-toolchain : Tạo ra tool SDK

meta-ide-support : cung cấp hỗ trợ cho các IDE như Eclipse

```
bitbake core-image-minimal
```

### Flash the image to BBB
Các image tool sẽ được sinh ra ở tmp/deploy/images/beaglebone-yocto

```
cd tmp/deploy/images/beaglebone-yocto
ls
```

Flash image , cần xóa hết dữ liệu ở thẻ nhớ trước 
```
dd if=core-image-minimal-beaglebone-yocto.wic of=/dev/sdb bs=4M
```
Đăng nhập root

### Thêm tool vào trong bbb
Có thể thấy ban đầu bbb sẽ không có git và bc giờ muốn thêm vào bằng 1 cách đơn giản là

Sửa file config

```
cd -
code conf/local.conf
```
Thêm đoạn code sau 

Các recipes và source  sau khi được tải từ mạng và biên dịch sẽ được đóng thành các package 

Có 2 kiểu install là IMAGE_INSTALL hoặc là IMAGE_INSTALL:append 

IMAGE_INSTALL : ghi mới hoàn toàn
IMAGE_INSTALL:append : ghi đè thêm phiển bản mới 

Để install nó vào image sẽ chạy lệnh sau.

```
IMAGE_INSTALL:append = " bc"
IMAGE_INSTALL:append = " git"
```

Biên dịch và nạp lại image mới 

```
bitbake core-image-minimal
```