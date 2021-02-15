# Shell Scripting

## Write iso to usb with dd

Find the drive:

```bash
sudo fdisk -l
```

Now write to the drive:

```bash
sudo dd bs=4M if=/path/to/the.iso of=/dev/sd[drive letter] status=progress oflag=sync
```

## Copy to clipboard in Linux

Source: [https://stackoverflow.com/a/5130969](https://stackoverflow.com/a/5130969)

```bash
cat file | xclip -selection clipboard
```


## Get just the filename

Source: [https://stackoverflow.com/a/12152669](https://stackoverflow.com/a/12152669)

```bash
$ echo "App.tsx" | cut -f 1 -d '.'
# returns "App"
```

- `-f` select which segment 
- `-d '.'` use `.` as the delimiter

## shebang / hashbang

Source: [https://itnext.io/upgrading-bash-on-macos-7138bd1066ba](https://itnext.io/upgrading-bash-on-macos-7138bd1066ba)


```bash
#!/usr/bin/env bash
echo "Using bash $BASH_VERSION"
```

Inspect the path and use the first encountered bash executable as the interpreter for the script. Good when the user has overridden the default version. E.g. I updated bash when I got a new MacBook.
