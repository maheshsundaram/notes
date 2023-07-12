# cut

```
ffmpeg -i input -ss hh:mm:ss -t hh:mm:ss -c:v copy -c:a copy output
```

where starting position follows `-ss` and duration follows `-t`

# download m3u8 streams

Given a directory of m3u8 master files:

```
for i in *.m3u8
do
  ffmpeg -protocol_whitelist file,http,https,tcp,tls,crypto -i $i -c copy -bsf:a aac_adtstoasc "${i%.m3u8}.mp4";
  rm $i;
done
```
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

https://bytefreaks.net/gnulinux/bash/ffmpeg-extract-audio-from-webm-to-mp3
