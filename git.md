# reflog

```bash
git reflog | fzf | awk '{print $1}' | xargs -I {} git reset --hard {}
```

or

``
git reflog # get commit id
git reset --hard $COMMIT_ID
```

# diff with fzf

``bash
FILE=$(fzf); git log --pretty=format:%H -- $FILE | fzf --ansi --preview "git show {-1} --color -- $FILE"
```

# show only file names

```bash
git show $commit --name-only
```

# remove untracked files

```bash
git clean -fdx
```

`-f` - force
`-d` - include directories
`-x` - remove ignored files
