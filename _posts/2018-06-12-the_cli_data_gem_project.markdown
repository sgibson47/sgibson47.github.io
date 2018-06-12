---
layout: post
title:      "The CLI Data Gem Project"
date:       2018-06-12 16:25:15 +0000
permalink:  the_cli_data_gem_project
---

The CLI Data Gem Project requires a student to use object-oriented ruby to build a CLI application that provides access to data scraped from a webpage.  The data has to go at least one level deep.  Most example projects show a user a list of items and provide a means for her to select from the list and get more detailed information about an item.  “For bonus points,” students are encouraged to go beyond creating an application to build a gem and publish it on rubygems.org. 

After reading through the requirements, I was of mixed feelings.  I knew I could create a CLI application made of object-oriented ruby that provided access to data from a webpage.  I had practiced modeling real world relationships, building a command line interface, and scraping websites in previous labs.  But, I also had reservations. Would I be able to find interesting information on an accessible website? How could I make my application a gem others could get and use?  

I settled on the Chicago Park District’s Events webpage because it offered information about movies playing in the city’s parks in the HTML I could see with chrome’s developer tools.  I pictured a CLI that let folks explore movie showings by a movie’s title, by the park in which a movie was playing, or by the date on which a showing was occurring. I submitted my idea and proposed website to scrape for approval and shifted gears.

I turned to researching how to build my own gem.  I reviewed RubyGems.org’s “Make Your Own Gem,” Bundler’s “How to create a Ruby gem with Bundler,” Flatiron’s CLI Gem Walkthrough video, and the GitHub Repos of the example projects before beginning to write my own code.  

Once my project idea was approved, I started building my gem. I setup my gem’s file structure, got some basic functionality (like printing out some text) added, and gave my new controller file executing permission.  At this point I could run the application by typing ./bin/[gem-name] into my terminal.  It wasn’t much, but it was working!

From here I broke and repaired the application over and over as I added or expanded features.  I built out the menu to take a user’s input and return a list of movies, parks, or dates depending on which the user had requested. I added a layer to each so that a user could ask for showing details on a particular movie showing, a list of movies playing in a given park, or a list of movies playing on a given date.  I also added another layer below the lists of movies playing in a park or on a certain date so a user could ask for the details of a particular showing from either list. All the information was fake, but I could follow the prompts down any of the available paths until I got to the details of a particular showing.

Once the CLI was working the way I wanted, I set about building three Ruby object classes to represent and organize the data I would later get from the web. I built Showing, Park, and Gate (ruby already has a Date class). Parks and Gates each had many showings, and each Showing belonged to instances of Park and Gate.  I was all set up to take in a collection of hashes that contained data representing different showings and use it to create showings, parks, and gates.  Then I went to build my scraper and hit what I thought was an impassable roadblock.  

When inspected the Chicago Park District’s webpage’s HTML in Chrome I could see the bit of content I wanted to access, links to pages about particular movie showings.  However, when I used Nokogiri to grab the page’s HTML there was something strange about the collection of objects I got back.  When I selected the container that held all the different movie showings, the Nokogiri object I got back was suspiciously short. 

Where I expected the Nokogiri object representing the container and numerous objects representing the movie showings, there was only the object representing the container.  I tried to select the link for a single showing and Nokogiri’s #css method returned an empty array.  I was flummoxed.  

After some googling and reading, I suspected that the content I was trying to collect was not in the website’s HTML to begin with, but rather had been added via JavaScript.  Sure enough, when I changed my Chrome settings to no longer allow JavaScript and reloaded the page, the content I wanted was gone.  

It was a frustrating.  I had gotten so far in the process of building my scraping gem.  But without the data about what movies were playing in the parks, a command line interface to make that information accessible was useless. 

Having learned my lesson, I went on the hunt for a new website to scrape.  This time without JavaScript enabled.  I found a couple of websites that publish a word of the day and quickly cranked out a CLI application that scrapped these websites, displayed a list of today’s words to the user, and let them select from the list to get more information on the word (definition and part of speech).  I followed the same process I had in my previous attempt.  I got the most basic thing running and then added to it bit by bit.  My new Words of the Day CLI met the minimum requirements for the project, but I was unsatisfied.  

I was still thinking about my Movies in the Park CLI.  I went back to the Park District’s website to see if I could find a path around my roadblock.  Sure enough there was a way.  While the Park District’s Events page relied on JavaScript to populate the information on upcoming events, its Nights Out in the Parks page did not. I could get the data I wanted to make the gem I had envisioned a reality!

While I could have submitted my Words of the Day CLI and been done with the project, I jumped at the chance to make the thing I had been planning.   I decided to make a fresh application and not use the code I had from my first attempt.  I wanted to apply what I had learned from building my Movies in the Park CLI   and from building the Words of the Day CLI into a final product I’d be happy to publish.

Thus, after a false start and a detour, I built Upcoming Movies in the Park.  It scrape’s the Chicago Park District’s website for information about upcoming movies playing in the city’s parks and lets a user explore that information by movie title, by park, or by date.  

Check out the code at https://github.com/sgibson47/upcoming_movies_in_the_park . 

Or, install the gem for yourself. In your terminal, type
```
gem install upcoming_movies_in_the_park
```  
and then enter 
```
upcoming_movies_in_the_park
```
and follow the prompts to learn about upcoming movies playing in Chicago’s parks!
