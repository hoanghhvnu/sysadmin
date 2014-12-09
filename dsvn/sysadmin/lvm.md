# lvm
Resize lvm
```
sudo lvresize -L+10GiB /dev/mapper/vg_sever-lv_home
sudo resize2fs /dev/mapper/vg_sever-lv_home
```
