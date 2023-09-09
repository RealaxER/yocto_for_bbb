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
