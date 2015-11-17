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
Cấu hình tối thiểu
 - RAM 4GB
 - HDD:60GB
 - NIC: 01: 192.168.1.1

Phiên bản hệ điều hành: Ubuntu Server 14.04 64 bit
 
```

- Đăng nhập bằng tài khoản root và thực hiện các lệnh sau để update bản mới nhất 
```sh
apt-get update -y && apt-get upgrade -y && apt-get dist-upgrade -y && init 6
```

##### Bước 2:
- Đăng nhập vào OS với tài khoản root.
- Tạo và gán cấu hình sudo cho user `stack`
```sh
adduser stack
apt-get -y install sudo 
echo "stack ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
```

- Chuyển từ tài khoản `root` sang tài khoản `stack` :
```sh
su - stack
```

##### Bước 3: Tải mã nguồn của OpenStack trong repos devstack
- Clone git của devstack về bằng lệnh git
- Mặc định (nhánh master) trong git của devstack là source mới nhất của OpenStack.
- Trong git của devstack chứa các phiên bản của OpenStack (Mitaka, Liberty, Kilo, Juno ...)
- Trong hướng dẫn này sẽ thực hiển tải bản OpenStack Liberty
```sh
git clone -b stable/liberty https://github.com/openstack-dev/devstack.git
```

#### Bước 4: Tạo file local.conf
- Các shell trong devstack sẽ tham chiếu tới file local.conf để lấy giá trị các `biến` khi thực thi các dòng lệnh trong shell đó.
- File local cần phải các các khao báo tối thiểu về password cho các dịch vụ trong OpenStack như: MySQL, RabbitMQ .... Các biến còn lại sẽ lấy giá trị mặc định.
- Tùy vào tính năng cần test mà người dùng có thể khai báo trong file local.conf
- Trong ví dụ này chúng tôi sử dụng dải mạng 192.168.1.0/24 cho dải EXTERNAL
- Di chuyển vào thư mục `devstack`
```sh
cd /devstack/
```

-Tạo file local.conf với nội dung mẫu như sau, lưu ý tùy vào tình huống mà có thể sửa file này cho phù hợp.
```sh
[[local|localrc]]
DEST=/opt/stack

# Khai bao log cho devstack
LOGFILE=$DEST/logs/stack.sh.log
VERBOSE=True
SCREEN_LOGDIR=$DEST/logs/screen
OFFLINE=False

# Khai bao IP cua may cai dat devstack
HOST_IP=192.168.1.110

# Khai bao mat khau cho cac dich vu
ADMIN_PASSWORD=vietstacker
MYSQL_PASSWORD=vietstacker
RABBIT_PASSWORD=vietstacker
SERVICE_PASSWORD=vietstacker
SERVICE_TOKEN=vietstacker


disable_service n-net
enable_service q-svc
enable_service q-agt
enable_service q-dhcp
enable_service q-meta
enable_service q-l3

#ml2
Q_PLUGIN=ml2
Q_AGENT=openvswitch

# vxlan
Q_ML2_TENANT_NETWORK_TYPE=vxlan

# Networking
FLOATING_RANGE=192.168.1.110.0/24
Q_FLOATING_ALLOCATION_POOL=start=192.168.1.150,end=192.168.1.200
PUBLIC_NETWORK_GATEWAY=192.168.1.1

FIXED_RANGE=172.16.0.0/24
NETWORK_GATEWAY=192.168.0.1

PUBLIC_INTERFACE=eth0

Q_USE_PROVIDERNET_FOR_PUBLIC=True
Q_L3_ENABLED=True
Q_USE_SECGROUP=True
OVS_PHYSICAL_BRIDGE=br-ex
PUBLIC_BRIDGE=br-ex
OVS_BRIDGE_MAPPINGS=public:br-ex


# Neu dung de dev thi thay enable_service cho dong duoi
disable_service tempest


#vnc
enable_service n-novnc
enable_service n-cauth
```
- Trong file khai báo trên đã thực hiện
 - Khai báo mật khẩu cho các dịch vụ
 - Kích hoạt Neutron cho devstack (từ bản Kilo trở đi, mặc định devstack sử dụng neutron thay cho nova-network)
 - Dải mạng External là: 192.168.1.0/24
 - Dải mạng Internet là: 172.16.0.0/24
 - Sau khi cài devstack với file local trên xong, devstack sẽ tạo ra dải mạng external và bắt đầu từ 192.168.1.150 tới 192.168.1.200
 - Thời gian cài đặt devstack có thể nhanh hay chậm tùy thuộc vào tốc độ internet. 
 - Có thể sử dụng các kỹ thuật repos, apt-cache để tăng tốc độ download các package khi cài devstack.
 
##### Các chú ý khi khởi động lại devstack
- Sau khi khơi động lại devstack cần thực hiện lệnh sau: `./home/stack/rejoin-devstack.sh`

