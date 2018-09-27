---
layout: post
title:      "My Recipe Box - Ruby on Rails Portfolio Project"
date:       2018-09-27 20:38:27 +0000
permalink:  my_recipe_box_-_ruby_on_rails_portfolio_project
---


The Rails Portfolio Project requires a student to build a Ruby on Rails application that manages related data through complex forms and RESTful routes.

I decided to create a tool I could use to keep track of recipes, ingredients, and times I made a particular recipe. I called it My Recipe Box.

My Recipe Box is a Ruby on Rails application that let’s registered users create records of ingredients, recipes that use those ingredients, and information about each time they make a recipe (a rating and notes).

Users must provide a unique email and a password to sign up or they can log in via Facebook. Once registered and logged in, users can create and view recipes, ingredients and makings.  Any user can edit an ingredients, but only the user who created a particular recipe or making may edit it. 

Check out the code at: https://github.com/sgibson47/my-recipe-box-2

Or, download My Recipe Box and start using it to keep track of your own recipes.

First, download your own copy of My Recipe Box. In terminal, run:

`git clone git@github.com:sgibson47/my-recipe-box-2.git`

Then from inside the my-recipe-box-2 directory, run:

`bundle install`

Followed by:

`rake db:migrate`

Followed by:

`thin start --ssl`

Then open up your browser and navigate to the web address provided in terminal after you entered shotgun – e.g. if terminal says "Listening on 0.0.0.0:3000," navigate to "https: ://localhost:3000."  Create an account or log in with Facebook and get started keeping track of ingredients you use, recipes you find, and what you make!


