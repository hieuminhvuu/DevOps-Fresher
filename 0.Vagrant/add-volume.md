# Thêm dung lượng volume cho máy ảo 

- khi làm lab đôi khi các máy ảo sẽ bị hết dung lượng, điều này dẫn đến ta sẽ ko thể cài thêm các pakage, tools, ... lên trên server đó nữa
- để có thể tăng dung lượng cho máy ảo ta follow theo các bước sau

1. Xác định thư mục Logical Volume (LV)

```
lvdisplay
```

<img src= images/001.png>

2. Dùng câu lệnh tăng dung lượng volume bằng đường dẫn đó

```
lvextend -L +2G /dev/ubuntu-vg/ubuntu-lv
```

Câu lệnh này sẽ tăng 2GB dung lượng cho volume tới ổ cứng vật lý

3. Resize cho filesystem

- Sau khi mở rộng volume hợp lý, bạn cần thay đổi kích thước filesystem để sử dụng không gian mới được phân bổ. Lệnh thay đổi kích thước filesystem tùy thuộc vào loại filesystem bạn đang sử dụng.
- ext4 filesystems :

```
resize2fs /dev/ubuntu-vg/ubuntu-lv
```

- XFS filesystems :

```
xfs_growfs /dev/ubuntu-vg/ubuntu-lv
```