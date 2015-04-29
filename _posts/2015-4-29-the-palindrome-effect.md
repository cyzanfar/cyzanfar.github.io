---
layout: post
title: Palindrome solver
---

Today I had a phone interview where I was asked to explain the logic I would implement to determine if a word is a [palindrome](http://en.wikipedia.org/wiki/Palindrome). A palindrome is a string that reads backwards and frontward. For example **civic** or [aibohphobia](http://www.rinkworks.com/words/palindromes.shtml).

Let's build an algorithm that will take a string and return true if it's a palindrome and false if it isn't.

Sweet!! Ruby has neat string method called `.reverse` that will fit just fine to solve the problem.

```Ruby
class Palindrome
	attr_accessor :word

	def initialize(word)
		self.word = word
	end

	def reversed_word
		word.reverse
	end

	def is_palindrome?
		 (word == reversed_word) ? true : false
	end
end
```

Here, we simply initialize our Palindrome class with a string and check whether the string passed in is equal to the reverse of that string.
Although it gets the job done, I don't think it helps me understand the true logic behind such a problem.

#### Breaking it down

Another way to think about a palindrome word is by breaking down the string into an array of characters each representing a position in the list. For example, the word "civic" split into an array gives us ["c","i","v","i","c"]. We can then check if the rear character "c" is equal to the front character "c" removing the rear and front every time condition is true. See [Figure](http://interactivepython.org/runestone/static/pythonds/BasicDS/PalindromeChecker.html) below.

![alt text](http://interactivepython.org/runestone/static/pythonds/_images/palindromesetup.png "palindrome")


Since we can `shift` and `pop` characters in the array, we can compare them and continue only if they match. We can keep matching the rear front of the array until we either run out of characters or be left with a array of size 1 depending on whether the length of the original string was even or odd.

```Ruby
class Palindrome

	def initialize(word)
		@word = word
	end

	def char_array
		@word.split("")
	end

	def pop_shift
		new_char = char_array
		answer = false
		while new_char.length > 1
			if new_char[0] == new_char[-1]
				answer = true
			else
				answer = false
				break
			end
			new_char.pop
			new_char.shift
		end
		answer
	end

end
```
There is definitely more code in this palindrome class, however, it does break down the problem further and helps us better understand the logical mechanism needed to check for a palindrome.

Go take a look at the source code on my [github](https://github.com/cyzanfar/Palindrome-solver) repository-- I also wrote some tests for it ;)

Happy Programming!






