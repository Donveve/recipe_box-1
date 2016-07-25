# Learning by Doing 

![Ubuntu version](https://img.shields.io/badge/Ubuntu-16.04%20LTS-orange.svg)
![Rails version](https://img.shields.io/badge/Rails-v5.0.0-blue.svg)
![Ruby version](https://img.shields.io/badge/Ruby-v2.3.1p112-red.svg)

Mackenzie Child's video really inspired me. So I decided to follow all of his rails video tutorial to learn how to build a web app. Through the video, I would try to build the web app by my self and record the courses step by step in text to facilitate the review.


# Project 3: How To Build A Recipe Box With Rails     

This time, we're going to build a Recipe Box. We're able to add recipes and users, as well as authentication. So only sign-in users are able to edit and destroy the recipe. If we click a recipes box on the root of this application, we can see image, title, description, multiple ingredients, and multiple directions on the show page. So we have a recipe model, an ingredients model, and an directions model. And then when we create a new recipe, we can upload our image. And we can add or remove multiple ingredients and can add or remove multiple direction step.

https://mackenziechild.me/12-in-12/3/         

### Highlights of this course
1. Users
2. Posts (Recipes)
3. Nested Forms
4. Image Uploading
5. HAML

# Create a Recipe Box
```console
$ rails new recipe_box
```

Chage directory to the recipe_box. Under `recipe_box/Gemfile`, add `gem 'therubyracer'`, save and run `bundle install`.      
Note: Because there is no Javascript interpreter for Rails on Ubuntu Operation System, we have to install `Node.js` or `therubyracer` to get the Javascript interpreter.
```console
$ bundle install
```

Then run the `rails server` and go to `http://localhost:3000` to make sure everything is correct.

Before we do anything, we do Git initialize:
```console
$ git init
$ git add .
$ git commit -am 'Initial Commit'
```