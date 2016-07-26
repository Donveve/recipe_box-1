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

### Using HAML
Instead of doing anything, I'll going to use HAML. To do this, we have to install it through the `gem`.

	Note:
	Haml (HTML Abstraction Markup Language) is a markup language that’s used to cleanly and simply describe the HTML of any web document, without the use of inline code. Once it’s installed, all view files with the ".html.haml" extension will be compiled using Haml. Haml supports Rails’ XSS protection scheme.

Under `recipe_box/Gemfile`, add `gem 'haml', '~>4.0.5'`, save and run `bundle install`. Then restart the server.

To get start it, let's go ahead and create a recipes
```console
$ rails g controller recipes
```
In addition to our controller, we're gonna to need some routes.
Under `app/config/routes.rb`
```ruby
Rails.application.routes.draw do
  resources :recipes

  root "recipes#index"
end
```
So back in our recipes controller, let's create a index action
Under `app/controllers/recipes_controller.rb`
```ruby
class RecipesController < ApplicationController
	def index
	end
end
```

Then under our views, we're obviously need a view for a index
Under `app/views/recipes`, we new a file named `index.html.haml`
```haml
%h1 This is the placeholder for the recipes#index
```

### Create a new recipe
Let's go ahead and create the ability to add our new recipes and show our recipes.
Under `app/controllers/recipes_controller.rb`
```ruby
class RecipesController < ApplicationController
	def index
	end

	def show
	end

	def new
	end

	def create
	end
end
```
I know each of the action we're gonna need to find the recipe, so I'm gonna to create a private method to hold that. And then I'm going to create a before action so that I can avoid the repeated code in each action.
Under `app/controllers/recipes_controller.rb`
```ruby
class RecipesController < ApplicationController
	before_action :find_recipe, only: [:show, :edit, :update, :destroy]
	...
	...

	private

	def find_recipe
		@recipe = Recipe.find(params[:id])
	end
end
```

Under our `new action` and `create action`, we're gonna want to do:
Under `app/controllers/recipes_controller.rb`
```ruby
class RecipesController < ApplicationController
	before_action :find_recipe, only: [:show, :edit, :update, :destroy]

	def index
	end

	def show
	end

	def new
		@recipe = Recipe.new
	end

	def create
		@recipe = Recipe.new(recipe_params)

		if @recipe.save
			redirect_to @recipe, notice: "Successfully created new recipe"
		else
			render 'new'
		end
	end

	private

	def recipe_params
		params.requre(:recipe).permit(:title, :description)
	end

	def find_recipe
		@recipe = Recipe.find(params[:id])
	end
end
```

Then we need to create our model.
```console
$ rails g model Recipe title:string description:text user_id:integer
$ rake db:migrate
```
Then let's go ahead to create the template
Under `app/views/recipes`, we new a file named `new.html.haml`, and the other is going to be `_form.html.haml`
In the `app/views/recipes/_form.html.haml`, we're going to use `simple_form`        
https://github.com/plataformatec/simple_form      

Before that, we need to add `simple_form` in our `Gemfile`
```
gem 'simple_form'
```

	Note: The `gem 'simple-form'` Mackenzie used in Rails4 will get error(undefined method 'number?') in Rails5,
	So I change the `gem 'simple_form'` to :

	gem 'simple_form', github: 'kesha-antonov/simple_form', branch: 'rails-5-0'


and run `bundle install`, then restart the server.
So we're going to user `simple_form`
Under `app/views/recipes/_form.html.haml`
```haml
= simple_form_for @recipe, html: { multipart: true } do |f|
	- if @recipe.errors.any?
		#errors
			%p
				= @recipe.errors.count
				Prevented this recipe froms saving
			%ul
				- @recipe.errors.full_messages.each do |msg|
					%li= msg
	.panel-body
		= f.input :title, input_html: { class: 'form-control' }
		= f.input :description, input_html: { class: 'form-control' }

	= f.button :submit, class: "btn btn-primary"
```

Then in our `new.html.haml` file, we're gonna do
`app/views/recipes/new.html.haml`
```haml
%h1 New Recipe

= render 'form'

%br/

= link_to "Back", root_path, class: "btn btn-default"
```

Then refresh the browser and go into `http://localhost:3000/recipes/new`, you will see
![image]()

To be continued...