---
layout: post
title: The N00b Stack
---

<!-- Next you can update your site name, avatar and other options using the _config.yml file in the root of your repository (shown below :point_down:). -->
Approximately a month ago, a friend of mine, Kimi, got an internship working as an assistant for this finance guy. I had just come back from a trip outside the country and was excited to hear about his new job; so I went straight to his place in Bushwick after I landed at the JFK airport. He was there sitting in front of his computer pulling out the last couple hair he had on his scalp. Then he said: “Dude…This guy is making me categories all the Senior Centers in the New York City area into one spreadsheet.”
Not sure how that relates to finance but sure, I felt his pain. So I observe for a few minutes his approach. He was literally going through them one by one, copy and pasting the name of the center, address, phone number and borough into his spreadsheet. What!? Hell no, there must be a quicker way to do this. At that time I didn’t know about the power of Nokorigi, but today is a different day and if I knew how to scrape then, this is what I would of done.
 THE NOOB SCRAPER
Scraping is the act of automating the process of extracting data from the internet.
Let's take a more interesting example than the Senior Centers in New York City. Let's say we want to extract the top 250 movie from [IMDB](http://www.imdb.com/chart/top?ref_=nv_ch_250_4)

Here is how:
Create a file called ```top_250_movie_imdb```. In the file, you need a way for your program to use the [Nokogiri](http://www.nokogiri.org/) library. At the top of your file ```require``` the Nokogiri gem as well as the ```open-uri``` gem, which is already pre-installed when you intall Ruby.
```Ruby
require 'Nokogiri'
require 'open-uri'```




![_config.yml]({{ site.baseurl }}/images/config.png)

The easiest way to make your first post is to edit this one. Go into /_posts/ and update the Hello World markdown file. For more instructions head over to the [Jekyll Now repository](https://github.com/barryclark/jekyll-now) on GitHub.
