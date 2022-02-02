1. get dimensions

```bash
ffprobe -v error -of flat=s=_ -select_streams v:0 -show_entries stream=height,width file.mov
```

2. convert frames to png

```bash
mkdir tmp
ffmpeg -i file.mov -vf scale=<width>:-1 -r 10 tmp/file%3d.png
```

3. join png files into gif

```bash
convert -resize 75% -delay 8 -dither none -coalesce -layers optimize -depth 8 -colors 128 -loop 0 tmp/file*.png file.gif
```
