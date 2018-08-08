---
layout: post
title:      "Who You Know - Sinatra Portfolio Project"
date:       2018-08-08 16:22:27 +0000
permalink:  who_you_know_-_sinatra_portfolio_project
---


The Sinatra Portfolio Project requires a student to use to build a Sinatra MVC application that lets users create an account with a unique attribute used to login and perform basic CRUD (create, read, update, delete) functions on records of resources.  Only the user that created a given record should be able to edit or delete it. 

I decided to create a tool I could use when networking to keep track of who I knew, where/when I interacted with them, and details I knew about them without that info being public.  I called it Who You Know.  

Who You Know is a Sinatra application that let’s registered users create records of contacts they meet, interactions with those contacts, and notes about the contact or the interaction.  

Users must provide a unique username, an email and a password to sign up.  Once registered and logged in, users can create, view, edit, and delete contacts, interactions, and notes that they created. 

Check out the code at:
https://github.com/sgibson47/who-you-know

Or, download Who You Know and start using it to keep track of your own contacts and interactions.  

First, download your own copy of Who You Know.  In terminal, run:

`git clone git@github.com:sgibson47/who-you-know.git`

Then from inside the who-you-know directory, run:

`rake db:migrate`

Followed by:

`shotgun`

Then open up your browser and navigate to the web address provided in terminal after you entered shotgun – e.g. http://127.0.0.1:9393/.  Create an account and enjoy an organized, private place to keep track of your contacts and interactions!
