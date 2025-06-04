### qemu-arch-att

https://wiki.archlinux.org/title/Installation_guide


```bash
image_dir=../images/qemu-archlinux
image=${image_dir}/archlinux-2025.06.01-x86_64.iso
disk=${image_dir}/arch.img
```


Create a disk drive, that is a file image:
```bash
$ qemu-img create ${disk} 10G
```

Proceed to the install
```bash
qemu-system-x86_64  -m 2G -cdrom ${image} -hda ${disk}
```

Not all options are needed especially when creating a test virtual machine, but one a nice convenience

```bash
# setfont ter-132b
```



---

Or proceed to the install, but try to add the user network
```bash
qemu-system-x86_64 -m 2G -cdrom ${image} -hda ${disk} -m 2G -net nic -net user,hostfwd=tcp::2222-:22
```


Then you can log into the system from the host:
```bash
# host > ssh -p 2222 italix@localhost
```


You could also use sftp when needed:
```bash
sftp localhost -oPort=2222
```

