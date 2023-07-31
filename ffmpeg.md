# cut

```
ffmpeg -ss hh:mm:ss -to hh:mm:ss -i input.mp4 -c copy output.mp4
```

where starting position follows `-ss` and duration follows `-t`

Sources:
- https://stackoverflow.com/a/42827058

# download m3u8 streams

Given a directory of m3u8 master files:

```
for i in *.m3u8
do
  ffmpeg -protocol_whitelist file,http,https,tcp,tls,crypto -i $i -c copy -bsf:a aac_adtstoasc "${i%.m3u8}.mp4";
  rm $i;
done
```

# concat

```
$ cat list.txt
file 'path1.mp4'
file 'path2.mp4'
file 'path3.mp4'

$ ffmpeg -f concat -safe 0 -i list.txt -c copy output.mp4
```

Sources:
- https://stackoverflow.com/a/11175851

# conversion

## webm -> mp4 -> mp2

```
ffmpeg -i file.webm -c copy file.mp4
ffmpeg -i file.mp4 -c:v mpeg2video -q:v 5 -c:a mp2 -f vob file.mpg
```

Sources:
- https://stackoverflow.com/a/60443156
- https://stackoverflow.com/a/46496070

## webm -> mp3

```
for FILE in *.webm; do
    ffmpeg -i "${FILE}" -vn -ab 128k -ar 44100 -y "${FILE%.webm}.mp3";
done
```

Sources:
- https://bytefreaks.net/gnulinux/bash/ffmpeg-extract-audio-from-webm-to-mp3
