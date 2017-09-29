# Ruby Cheats
## Download File
this properly handle redirects
```
require 'open-uri'

url = 'http://foo.bar'
output_file = 'path/to/file'

File.open(output_file, "w") do |f|
  IO.copy_stream(open(url), f)
end
```
## Execute Process
There's about 50 different ways to do this in ruby.  Googling on this is quite disorienting.  Below are the approaches that have all worked for me just fine.  They are work the same as best I can tell.

```
puts %x( some command string )
```

```
def run(cmd)
  system(cmd) || raise "command '#{cmd}' failed"
end
```
