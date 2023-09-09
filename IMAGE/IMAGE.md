# HƯỚNG DẪN BUILD YOCTO 

### Mở config
/yocto/bbb/build$
```
code conf/local.conf
```
### Thêm đoạn mã sau

```
IMAGE_INSTALL:append = " hello"
IMAGE_INSTALL:append = " example"
```
### Thêm vào .bb của image 
Thường trong dự án người ta sẽ tạo ra một meta-layer và trong đó sẽ đóng gói các package và recipe và đẩy nó lên source 

Chứ không đẩy mỗi file.conf

Về bản chất thêm vào .conf vẫn sẽ chạy đúng 

Khi bitbake chạy nó sẽ tìm các file .conf sau đó tìm các file .bb nên thêm ở đâu cũng chạy được

```
cd yocto/poky-kirkstone/meta
ls
```
Đây là nơi chứa recipe cho image 
```
ls recipes-core/images/
```
Chúng ta sẽ có file core-image-minimal.bb

```
code recipes-core/images/core-image-minimal.bb
```
Thêm đoạn mã sau để nó biên dịch vào image
```
IMAGE_INSTALL:append = " hello"
IMAGE_INSTALL:append = " example"
```