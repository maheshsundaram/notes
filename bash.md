# Bash / Shell

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

## Move files

```bash
for file in *; do git mv "$file" "${file%.js}.ts"; done;
```

https://stackoverflow.com/a/1225236

## Loop

```bash
for i in "${arrayName[@]}"
do
   echo $i
done
```

or 

```bash
for i in {1..10}; do echo $i; done
```

## Read file line by line

```bash
while IFS= read -r line; do
    echo "$line"
done < filename.txt
```

## Datetime

```bash
echo "$(date '+%Y-%m-%dT%H:%M:%S')"
```
