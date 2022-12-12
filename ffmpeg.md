# cut

```
ffmpeg -i input -ss hh:mm:ss -t hh:mm:ss -c:v copy -c:a copy output
```

where starting position follows `-ss` and duration follows `-t`
