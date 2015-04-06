---
layout: post
title: Building the Josephine Gem
---

Recently I've been starting to mess around with Ruby Gems and try making one of my own. It is always a good feeling to see that the gem you made is being used by people across the world. Ahhhh... The beauty of open source software :).

Ok, so it's time for us to make our first Gem, Josephine (my girlfriend name AKA hopeless romantic). We are going to build it from scratch without using the `bundler` gem which  auto-populate the files and folders you'll need and initialize a git repository for you. A good reference for building gems is the [RubyGems.org](http://guides.rubygems.org/make-your-own-gem/) website. The [RubyGems.org](https://rubygems.org/) is the Ruby community's gem hosting service. I will publish my gem there and anybody can view it and install it from the website.

What is Josephine? well, it's a Ruby gem that calculates number of files, total lines of code, comments, line spacing and average LOC.

```
% tree
.
├── josephine.gemspec
└── lib
    └── josephine.rb
```
The convention when building a gem is to have ***one*** file in your **lib** directory with the same name as your gem. `josephine.rb` is in charge of setting up your gem's code and API.


**josephine.rb**

```Ruby
class Josephine
def initialize(extension)
@extension = extension
extension_file
count_lines
end

def extension_file
if @extension == "-all".strip
ext = File.join("**", "*")
files = Dir.glob(ext)
else
ext = File.join("**", "*#{@extension}")
files = Dir.glob(ext)
end
end

def count_lines
num_files = 0 # Number of files
line_count = 0 # Number of lines of code
line_com = 0 # Number of lines of comments
space = 0
extension_file.each do |f|
next if f.index('vendor')
next if FileTest.directory?(f)
num_files += 1
i = 0
lines = []
File.new(f).each_line do |line|
if line.scrub.strip[0] == nil
space += 1
next
end
if line.scrub.strip[0] == '#'
line_com += 1
next
end

i += 1
end


line_count += i
end
puts ""
puts "------------------------------"
if @extension == "-all"
puts "#{num_files.to_s} files"
else
puts "#{num_files.to_s} files with #{@extension} extension."
end
puts "#{line_count.to_s} lines of code."
puts "#{(line_count.to_f/num_files.to_f).round(2)} LOC/file."
puts "#{line_com.to_s} lines of comments."
puts "#{space.to_s} line spacing."
puts "#{(space.to_f / num_files.to_f).round(2)} average line space / file."

end
end
```

In the **josephine.gemspec**


```Ruby
Gem::Specification.new do |s|
  s.name        = 'josephine'
  s.version     = '0.0.0'
  s.date        = '2015-04-05'
  s.summary     = "I <3 Josephine "
  s.description = "count the number of lines of code in your project files"
  s.authors     = ["Cyrus Ghazanfar"]
  s.email       = 'cghazanfar10@gmail.com'
  s.files       = ["lib/josephine.rb"]
  s.homepage    =
    'http://rubygems.org/gems/josephine'
  s.executables << 'josephine'
  s.license       = 'MIT'
end
```

The gem specification file defines what’s in the gem, who made it, and the version of the gem. It is also your interface to communicate with [Rubygems.org](https://rubygems.org/).


Since this gem will be run from the command line, we need to specify an executable file in a **bin** directory.


```
% tree
.
├── josephine.gemspec
└── bin
|    └── josephine
└── lib
    └── josephine.rb
```


In **bin/josephine**

```Ruby
require 'josephine'
ARGV.each do |ext|
	puts Josephine.new(ext)
end
```

Here `ARGV` is the argument passed to josephine when executing the gem from the command line.
For example, if I want to know the number of lines of code in all the files with `.rb` extensions, then I will run


`$ josephine .rb`


`.rb` is the `ARGV` passed to josephine.


Now that you have created a gemspec, you can build a gem from it.


```
$ gem build josephine.gemspec
		Successfully built RubyGem
		Name: hola
		Version: 0.0.0
		File: josephine-0.0.0.gem
```

```
$ gem install ./josephine-0.0.0.gem
		Successfully installed hola-0.0.0
		1 gem installed
```

To share your gem on the [RubyGems](https://rubygems.org/) you need to [create and account](https://rubygems.org/sign_up) and set up your computer with you RubyGems account via this command:


```
$ curl -u qrush https://rubygems.org/api/v1/api_key.yaml >
~/.gem/credentials; chmod 0600 ~/.gem/credentials
```

when your are finish with the setup, you can publish your gem to RubyGems

```
$ gem push josephine-0.0.0.gem
		Pushing gem to RubyGems.org...
		Successfully registered gem: josephine (0.0.0)
```





