---
layout: post
title:      "Protecting Your API Key on GitHub"
date:       2019-04-05 18:33:52 -0400
permalink:  protecting_your_api_key_on_github
---


Let’s say you want to create an application that will make use of another entity’s API.  You’ll probably need to get a key or a token from that entity in order to access the API.  The key tells the API provider who is using the API and how (i.e. what information are they requesting, how often are they making requests, . . . etc.). 

You don’t want other people to have access to your application’s key because they may abuse it by exceeding the API’s rate limit or violating its terms of service in some other way.  That could result in your key being revoked by the API provider and then your application won’t work. You’re also likely using GitHub to back up your application and track changes as you build.  

Oh no, public git repositories can be accessed by anyone. That means that the contents of your application and your API key are going to be visible to everyone! Don’t worry.  In this post I’m going to walk you through how to protect your API so that it isn’t made public to folks on GitHub. 

## Use dotenv
Dotenv is a module that loads environment variables from a `.env` file into `process.env`. We’re going to use dotenv and store our API key in a .env file that we’ll tell git to ignore so that its contents aren’t stored and made public in our repository. 

### Install dotenv

` npm install dotenv --save`

### Require dotenv
As early as you can in your app, add:

` require('dotenv').config()`

### Create a .gitignore file
If you don’t have one already, create a file in your application called `.gitignore`. 

Add `.env` to it.

This ensures that the `.env` file, and the API key we’ll store in it, won’t be included in the project’s git repository.  

### Create a .env file
Create a file called `.env`.

### Add your API Key to .env
Inside your .env file, declare a variable and store your API key in it.

` MY_API_KEY='123456'`

Now your API will be accessible throughout your application at ` process.env.MY_API_KEY`. 


## That’s it!
Now you can make use of the API key you procured without exposing it to everyone when you store your application on GitHub.

