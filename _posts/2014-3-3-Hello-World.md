---
layout: post
title: The N00b Stack
---

<!-- Next you can update your site name, avatar and other options using the _config.yml file in the root of your repository (shown below :point_down:). -->
Approximately a month ago, a friend of mine, Kimi, got an internship working as an assistant for this finance guy. I had just come back from a trip outside the country and was excited to hear about his new job; so I went straight to his place in Bushwick after I landed at the JFK airport. He was there sitting in front of his computer pulling out the last couple hair he had on his scalp. Then he said: “Dude…This guy is making me categories all the Senior Centers in the New York City area into one spreadsheet.”
Not sure how that relates to finance but sure, I felt his pain. So I observe for a few minutes his approach. He was literally going through them one by one, copy and pasting the name of the center, address, phone number and borough into his spreadsheet. What!? Hell no, there must be a quicker way to do this. At that time I didn’t know about the power of Nokorigi, but today is a different day and if I knew how to scrape then, this is what I would of done.

 **Scraping for Noobs**

Scraping is the act of automating the process of extracting data from the internet.
Let's take a more interesting example than the Senior Centers in New York City. Let's say we want to extract the top 250 movie from [IMDB](http://www.imdb.com/chart/top?ref_=nv_ch_250_4)

Here is how:

Create a file called ```top_250_movie_imdb```. In the file, you need a way for your program to use the [Nokogiri](http://www.nokogiri.org/) library. At the top of your file ```require``` the Nokogiri gem as well as the ```open-uri``` gem, which is already pre-installed when you intall Ruby.

```Ruby
require 'Nokogiri'
require 'open-uri'
```

If you look in the Nokogiri documentation, you'll see that there is a special way to parse a page from the internet. Here I will create a variable called `imdb` in which I will save the parsed content of the top 250 movie page. The `imdb` now refers to the parsed webpage. We will use the specific formatting that Nokogiri provides in order to iterate through the page and save the content into a Ruby hash.

So far, our file looks like this:

```Ruby
require 'Nokogiri'
require 'open-uri'
html = File.read('fixtures/movie.html')
	imdb = Nokogiri::HTML(open("http://www.imdb.com/chart/top"))
```
Now, we need to declare our hash that will them contain the different element we want to scrap from this page:


```Ruby
movie = {}
```
 This is the part where it gets a little tricky...
 Go to the [IMDB top 250 movie page](http://www.imdb.com/chart/top?ref_=nv_ch_250_4) and start inspecting the elements (under developer tools).

 What exactly are we looking for here? Well think about it-- we need to iterate over each row and for each movie title we want the rating and the year it was released.
 What do think? There is a `tbody` element with a class of 	`lister_list` which selects the entire row. We see that each row inside of the `tbody` element has a `tr` of class `odd`. Thats what we need!

 Here is how to select and iterate through the table row:

```Ruby
 imdb.css("tbody.lister-list tr.odd").each do |film|
		title = film.css("td.titleColumn a").text
		movie[title] = {
			:image_link => film.css("td.posterColumn a img").attribute("src").value,
			:date => film.css("td.titleColumn span.secondaryInfo").text,
			:rating => film.css("td.ratingColumn strong").text
		}
	end
```
Here we iterated through each table row making the title of the movie a key and then another nested array containing the different key value pair like rating, a link to the image of the movie and the year it came out.

Here is how the first row looks like:

```Ruby
{"The Shawshank Redemption"=>
  {:image_link=>
    "http://ia.media-imdb.com/images/M/MV5BODU4MjU4NjIwNl5BMl5BanBnXkFtZTgwMDU2MjEyMDE@._V1_SX34_CR0,0,34,50_AL_.jpg",
   :date=>"(1994)",
   :rating=>"9.2"}
```
