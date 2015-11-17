### Các ghi chép và hướng dẫn về devstack
- Các ghi chép hướng dẫn chạy devstack

#### Devstack là gì ???
- Tạm gọi devstack là OpenStack theo kiểu "mì tôm"
- Được sử dụng để cài đặt OpenStack native, hay sử dụng bởi các deverloper.
- Devstack KHÔNG được khuyến cáo để triển khai trong các môi trường Production

#### Các đặc điểm & ưu điểm của devstack
- Devstack được viết hoàn toàn bằng shell.
- Devstack cài đặt từ source bằng các tải trực tiếp các package từ GIT
- Devstack triển khai nhanh gọn, thường chạy với mô hình AIO
- Devstack có thể được sử dụng để cài các phiên bản của OpenStack (Liberty, Kilo, Juno ...). Nếu sử dụng nhánh master của GIT thì là bản mới nhất của OpenStack.
- Devstack thường được sử dụng để deverlop hoặc test các tính năng trong các phiên bản của OpenStack

#### Hướng dẫn cài đặt devstack.
- Trên một node

##### Bước 1:
- Chuẩn bị một máy Ubuntu 14.04 Server 64 bit với cấu hình
```sh
- Cấu hình tối thiểu
 - RAM 4GB
 - HDD:60GB
 - NIC: 01: 192.168.1.1
```

- Đăng nhập bằng tài khoản root và thực hiện các lệnh sau để update bản mới nhất 
