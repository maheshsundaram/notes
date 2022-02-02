# cargo

to fix 
```
  * attempted ssh-agent authentication, but no usernames succeeded: `git`
```

do

```
eval `ssh-agent -s` && ssh-add
```

# nightly

`rustup update nightly`
