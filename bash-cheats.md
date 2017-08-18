# Bash Cheats

## iterating an array
```
names=( foo bar baz )
for i in "${names[@]}"
do
  echo $i
done
```
