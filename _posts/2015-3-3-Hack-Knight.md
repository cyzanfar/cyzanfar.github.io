---
layout: post
title: Object Oriented Multiples
---

Today at the [Flatiron School](http://flatironschool.com/) we were introduced to a simple code challenge to prep us for the eventual future technical interview.
I would imagine that the challenge is timed and needs to be completed within a specific timeframe.
Let's put ourselves in the shoes of a recently graduated Flatiron student whose job is now to look for a job!
 - Ralph is an employer at a small tech company. He is here to give you the code challenge and watch you complete it.
 - Ralph "Write a script that sums all the multiples of 3 or 5 between 0 and any given number"

 <h4>Think...Think...</h4>
 Ok, so first multiples of 3 or 5 is any number that has a remainder of 0 when divided by 3 or 5. Got it!
 Seems simple... Now we need a way to sum all of the multiples.

 Here is how we start:
 	<h5>1st step</h5
 Think object oriented. You want a class called `Multiples` for example, that takes a argument (the limit) when initialized.

 ```Ruby
 class Multiples
 	attr_accessor :limit, :multiples
 	def initialize(limit)
 		@limit = limit
 		@multiples = []
 	end
 end
 ```
The instance variable `multiples` is an array that will eventually contain all the multiples of 3 or 5 between 0 and the limit `@limit`.
	<h5>2nd step</h5>
Our first method should take care of one think and one think only: collecting all the multiples of 3 or 5 and storing them into the array `@multiples`

```Ruby
def collect_multiples
	counter = 1
	while counter < @limit
		if counter % 3 == 0 || counter % 5 == 0
				@multiples << counter
		end
		counter +=1
	end
	@multiples
end
```
Here we are using the comparator operator `||` (or) to check the value of the local variable `counter` and push the result into the array `@multiples`
Remember that the method returns the last expression evaluated, so `@multiples` needs to be placed before the closure of the method `collect_multiples`
	<h5>3rd step</h5>
	Finally, we need to take the result of `collect_multiples` (which is an array) and sum all the elements in it.
```Ruby
	def sum_multiples
		collect_multiples.inject {|sum, x| sum + x}
	end
```

For the final step, let's prompt the user for the limit:
```Ruby
class Multiples
	attr_accessor :limit, :start, :multiples
	def initialize(limit)
		@limit = gets.chomp.to_i # prompt user and convert input into an Fixnum
		@multiples = []
	end
	def collect_multiples
		counter = 1
		while counter < @limit
			if counter % 3 == 0 || counter % 5 == 0
				@multiples << counter
			end
			counter +=1
		end
		@multiples
	end
	def sum_multiples
		collect_multiples.inject { |sum, x| sum + x}
	end
end
n = Multiples.new(@limit) # create new instance of the class Multiples
puts "#{n.sum_multiples}" # print the sum of the multiples
```
Try it out!
In your bash run `ruby name_of_file.rb` and voila!
If you give a limit of 1000 for example, you should get.... 233168. Bravo! you got it ;)

There is a great website for code challenges. Visit [coderbyte](http://coderbyte.com/) choose your language of preference and get started!




