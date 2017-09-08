# Bash Cheats

## iterating an array
```
names=( foo bar baz )
for i in "${names[@]}"
do
  echo $i
done
```
## file bytes
Sometimes you need to know how many bytes a file contains - this is useful for
troubleshooting things like crypto keys getting whitespace appended accidentally, etc.

```
$ wc -c path/to/file
     344 path/to/file
```
