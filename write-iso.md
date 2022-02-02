# Write iso to usb with dd

Find the drive:

```bash
sudo fdisk -l
```

Now write to the drive:

```bash
sudo dd bs=4M if=/path/to/the.iso of=/dev/sd[drive letter] status=progress oflag=sync
```
