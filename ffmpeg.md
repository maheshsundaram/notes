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
