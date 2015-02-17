dm_crypt
==========
Chris Sivanich ©2015

----------

#### Creating device

    cryptsetup -v --cipher aes-xts-plain64 --key-size 512 --hash sha512 --iter-time 5000 --use-random luksFormat <device> [ key ]

#### Creating key

    mkdir -m 700 /etc/luks-keys
    dd if=/dev/random of=/etc/luks-keys/home bs=1 count=256
    The logical volume is encrypted with it:
    cryptsetup luksFormat -v -s 512 /dev/lvm/home /etc/luks-keys/home
    cryptsetup -d /etc/luks-keys/home open --type luks /dev/lvm/home home
    mkfs -t ext4 /dev/mapper/home
    mount /dev/mapper/home /home
