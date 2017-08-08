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
