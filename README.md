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

Note: 
Because there is no Javascript interpreter for Rails on Ubuntu Operation System, we have to install `Node.js` or `therubyracer` to get the Javascript interpreter.

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
	Haml (HTML Abstraction Markup Language) is a markup language 
	that’s used to cleanly and simply describe the HTML of any web document, without the use of inline code. 
	Once it’s installed, all view files with the ".html.haml" extension will be compiled using Haml. 
	Haml supports Rails’ XSS protection scheme.

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
I know each of the action we're gonna need to find the recipe, so I'm gonna to create a private method to hold that. 
And then I'm going to create a before action so that I can avoid the repeated code in each action.

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
		params.require(:recipe).permit(:title, :description)
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

	Note:      
	The " gem 'simple-form' " Mackenzie used in Rails4 will get error(undefined method 'number?') in Rails5,
	So I change it to :

	gem 'simple_form', github: 'kesha-antonov/simple_form', branch: 'rails-5-0'


and run `bundle install`, then restart the server.

So we're going to use `simple_form`      

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
![image](https://github.com/TimingJL/recipe_box/blob/master/pic/new_recipe_form.jpeg)


And we have to create a show page to show our recipe post.

Under `app/views/recipes`, we new a file named `show.html.haml`   
And list out the title and description

```haml
%h1= @recipe.title
%p= @recipe.description

=link_to "Back", root_path, class: "btn btn-default"
```

Note:
`%p= @recipe.description` is unable to do text wraps, so I tweak the code to `%p= simple_format @recipe.description`.
```haml
%h1= @recipe.title
%p= simple_format @recipe.description

=link_to "Back", root_path, class: "btn btn-default"
```


In our `app/controllers/recipes_controller.rb`, we want to list our all of our recipes.
```ruby
def index
	@recipe = Recipe.all.order("created_at DESC")
end
```
Now, inside of our `app/views/recipes/index.html.haml`, let's create a loop.
So the loop list out each of our recipes.
```haml
- @recipe.each do |recipe|
	%h2= link_to recipe.title, recipe
```

So we have the ability to create a post. And then we'll add the ability to add and destroy as well.

### Ability to Destroy posts
Under `app/controllers/recipes_controller.rb`
```ruby
class RecipesController < ApplicationController
	before_action :find_recipe, only: [:show, :edit, :update, :destroy]

	...
	...

	def edit
	end

	def update
		if @recipe.update(recipe_params)
			redirect_to @recipe
		else
			render 'edit'
		end
	end

	def destroy
		@recipe.destroy
		redirect_to root_path, notice: "Successfully delted recipe"
	end	

	private

	def recipe_params
		params.require(:recipe).permit(:title, :description)
	end

	def find_recipe
		@recipe = Recipe.find(params[:id])
	end
end
```

In `edit` we don't need to do anything, because in `before action` it find recipe for us.

Then, let's create a `edit.html.haml` page under `app/views/recipes`     
Under `app/views/recipes/edit.html.haml` 
```haml
%h1 Edit Recipe

= render 'form'
```
Then back to our `show.html.haml`, let's add a edit link and a destroy link      
In `app/views/recipes/show.html.haml`
```haml
%h1= @recipe.title
%p= simple_format @recipe.description

=link_to "Back", root_path, class: "btn btn-default"
=link_to "Edit", edit_recipe_path, class: "btn btn-default"
=link_to "Delete", recipe_path, method: :delete, data: {confirm: "Are you sure?"}, class: "btn btn-default"
```
So we got the basic functionality of our Recipe.
![image](https://github.com/TimingJL/recipe_box/blob/master/pic/basic_functionality.jpeg)

# Styling using bootstrap
Let's add some bootstrap.

We need to add bootstrap gem in our Gemfile, run `bundle install` and restart server.
```
gem 'bootstrap-sass', '~> 3.2.0.2'
```

	Note:
	File to import not found or unreadable: 
	bootstrap-sprockets Only for Twitter Bootstrap 3, bootstrap-sprockets is used.

And if you go to the github page of the bootstrap-sass, it would give you some information on what you need to do.
https://github.com/twbs/bootstrap-sass       

First, we need to go into `app/assets/stylesheets` and rename `application.css` to `application.css.scss`.       
And then, we gonna to import Bootstrap styles in `app/assets/stylesheets/application.css.scss`:

	@import "bootstrap-sprockets";
	@import "bootstrap";

![image](https://github.com/TimingJL/recipe_box/blob/master/pic/basic_styling.png)

### Add some structure

Before anything, we rename `app/views/layouts/application.html.erb` to `app/views/layouts/application.html.haml`.     
And change html code to haml code:
```haml
!!! 5
%html
%head
	%title RecipeBox
	= csrf_meta_tags
	= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload'
	= javascript_include_tag 'application', 'data-turbolinks-track': 'reload'
%body
	= yield
```

Let's add some structure to our applicaiton.

### Navbar
First, I'm going to add Navbar.     
Under `app/views/layouts/application.html.haml`
```haml
!!! 5
%html
%head
	%title RecipeBox
	= csrf_meta_tags
	= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload'
	= javascript_include_tag 'application', 'data-turbolinks-track': 'reload'
%body
	%nav.navbar.navbar-default
		.container
			.navbar-brand= link_to "Recipe Box", root_path

			%ul.nav.navbar-nav.navbar-right
				%li= link_to "New Recipe", new_recipe_path
				%li= link_to "Sign Out", root_path

	.container
		- flash.each do |name, msg|
			= content_tag :div, msg, class: "alert"

		= yield
```
![image](https://github.com/TimingJL/recipe_box/blob/master/pic/navbar.jpeg)


### Add images

Let's go ahead and add images to the recipes.

To do that, we're going to use `paperclip` gem.       
https://github.com/thoughtbot/paperclip

We need to add `paperclip` gem in our Gemfile, run `bundle install` and restart server.            
In `Gemfile`, we add
```
gem 'paperclip', '~> 4.2.0'
```

In our recipe model `app/models/recipe.rb` (Ref: `Quick Start` of Github paperclip)
```ruby
class Recipe < ApplicationRecord
	has_attached_file :image, styles: { medium: "400x400#" }
	validates_attachment_content_type :image, content_type: /\Aimage\/.*\Z/
end
```

Next, we need to add migration
```console
$ rails g paperclip recipe image
$ rake db:migrate
```

Next, we need to add this to our form.       
So in `app/views/recipes/_form.html.haml`
```haml
...
...
	.panel-body
		= f.input :title, input_html: { class: 'form-control' }
		= f.input :description, input_html: { class: 'form-control' }
		= f.input :image, input_html: { class: 'form-control' }
...
...
```

And in our recipes controller `app/controllers/recipes_controller.rb`, we need to add that to our recipe params.
```ruby
...
...
def recipe_params
	params.require(:recipe).permit(:title, :description, :image)
end
...
...
```

In `app/views/recipes/show.html.haml`
```haml
= image_tag @recipe.image.url(:medium, class: "recipe_image")
%h1= @recipe.title
%p= simple_format @recipe.description

=link_to "Back", root_path, class: "btn btn-default"
=link_to "Edit", edit_recipe_path, class: "btn btn-default"
=link_to "Delete", recipe_path, method: :delete, data: {confirm: "Are you sure?"}, class: "btn btn-default"
```



Then, restart the server and refresh the browser.
![image](https://github.com/TimingJL/recipe_box/blob/master/pic/paperclip.jpeg)

![image](https://github.com/TimingJL/recipe_box/blob/master/pic/image_post.jpeg)

And then, we want to add that in our `app/views/recipes/index.html.haml` as well.
For each recipe, we want the image tag, and this would be the link as well.
```haml
- @recipe.each do |recipe|
	= link_to recipe do
		= image_tag recipe.image.url(:medium)
	%h2= link_to recipe.title, recipe
```

It works well, but it looks not very well. So let's add some structure to our index page.
To do this with bootstrap, we need to use the `each slice` method.
Under `app/views/recipes/index.html.haml`
```haml
- @recipe.each_slice(3) do |recipes|
	.row
		- recipes.each do |recipe|
			.col-md-4
				.recipe
					.image_wrapper
						= link_to recipe do
							= image_tag recipe.image.url(:medium)
					%h2= link_to recipe.title, recipe
```
![image](https://github.com/TimingJL/recipe_box/blob/master/pic/index_image_structure.jpeg)

### Styling
Under `app/assets/stylesheets/application.css.scss`
```scss
/*
 * This is a manifest file that'll be compiled into application.css, which will include all the files
 * listed below.
 *
 * Any CSS and SCSS file within this directory, lib/assets/stylesheets, vendor/assets/stylesheets,
 * or vendor/assets/stylesheets of plugins, if any, can be referenced here using a relative path.
 *
 * You're free to add application-wide styles to this file and they'll appear at the bottom of the
 * compiled file so the styles you add here take precedence over styles defined in any styles
 * defined in the other CSS/SCSS files in this directory. It is generally better to create a new
 * file per style scope.
 *
 *= require_tree .
 *= require_self
 */

@import "bootstrap-sprockets";
@import "bootstrap";

@mixin box-shadow {
	-webkit-box-shadow: rgba(0, 0, 0, 0.09) 0 2px 0;
	-moz-box-shadow: rgba(0, 0, 0, 0.09) 0 2px 0;
	box-shadow: rgba(0, 0, 0, 0.09) 0 2px 0;
}

$red: #DB6161;

body {
	background: rgb(235, 238, 243);
}

.main_content {
	padding: 0 0 50px 0;
}

.alert {
	padding: 15px;
	margin-bottom: 20px;
	border: 1px solid transparent;
	border-radius: 5px;
	@include box-shadow;
	background: white;
	font-weight: bold;
}

.navbar {
	margin-bottom: 50px;
	@include box-shadow;
	border: none;
	.navbar-brand {
		text-transform: uppercase;
		letter-spacing: -1px;
		font-weight: bold;
		font-size: 25px;
		a {
			color: $red;
		}
	}
}

.recipe {
	background: white;
	border-radius: 5px;
	margin-bottom: 40px;
	@include box-shadow;
	.image_wrapper {
		max-width: 100%;
		border-radius: 5px 5px 0 0;
		overflow: hidden;
	}
	img {
		width: 100%;
		-webkit-transition: all .3s ease-out;
	  -moz-transition: all .3s ease-out;
	  -o-transition: all .3s ease-out;
    transition: all .3s ease-out;
		&:hover {
			transform: scale(1.075);
		}
	}
	h2 {
		padding: 15px 5%;
		margin: 0;
		font-size: 20px;
		font-weight: normal;
		line-height: 1.5;
		a {
			color: $red;
		}
	}
}

#recipe_top {
	margin-bottom: 50px;
}

#recipe_info, #ingredients, #directions {
	background: white;
	@include box-shadow;
	min-height: 360px;
	border-radius: 5px;
	padding: 2em 8%;
}

.recipe_image {
	max-width: 100%;
	border-radius: 5px;
	@include box-shadow;
}

#recipe_info {
	h1 {
		font-size: 36px;
		font-weight: normal;
		color: $red;
	}
	.description {
		color: #8A8A8A;
		font-size: 20px;
	}
}

#ingredients, #directions {
	margin-bottom: 50px;
	ul, ol {
		padding-left: 18px;
		li {
			padding: 1em 0;
			border-bottom: 1px solid #EAEAEA;
		}
	}
}

.form-inline {
	margin-top: 15px;
}
.form-input {
	width: 65% !important;
	float: left;
}
.form-button {
	float: left;
	width: 30% !important;
	margin-left: 5%;
}
.add-button {
	margin-top: 25px;
}
```

Refresh the browser, and then you will see
![image](https://github.com/TimingJL/recipe_box/blob/master/pic/index_styling.jpeg)


# Add Ingredients and Directions
So next, we need to add the ability to have `Ingredients` and `Directions` for each one of our recipes.
The way we gonna to do is by using `gem 'cacoon'`. Cacoon make it easy to have the nested form.
https://github.com/nathanvda/cocoon      

In `Gemfile`
```
gem 'cocoon', '~> 1.2.6'
```
Run `bundle install` and restart the server.

In `app/javascripts/application.js`, we add `//= require cocoon` under `//= require jquery_ujs`
```js
//= require jquery
//= require jquery_ujs
//= require cocoon
//= require turbolinks
//= require_tree .
```

First, we need to create a model for our ingredients, and then the model for the directions.
```console
$ rails g model Ingredient name:string recipe:belongs_to
$ rails g model Direction step:text recipe:belongs_to
$ rake db:migrate
```
And it created the Ingredient table and Direction table.

Then under recipe model, `app/models/recipe.rb`
```ruby
class Recipe < ApplicationRecord
	has_many :ingredients
	has_many :directions


	accepts_nested_attributes_for :ingredients,
								reject_if: proc { |attributes| attributes['name'].blank? },
								allow_destroy: true
 	accepts_nested_attributes_for :directions,
								reject_if: proc { |attributes| attributes['step'].blank? },
								allow_destroy: true

	validates :title, :description, :image, presence: true

	has_attached_file :image, styles: { medium: "400x400#" }
	validates_attachment_content_type :image, content_type: /\Aimage\/.*\Z/
end
```

And then we need to add attribute to our strong parameters insdie of our controller.        
In `app/controllers/recipes_controller.rb`
```ruby
...
...

def recipe_params
	params.require(:recipe).permit(:title, :description, :image, ingredients_attributes: [:id, :name, :_destroy], directions_attributes: [:id, :step, :_destroy])
end

...
...
```

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
		= f.input :image, input_html: { class: 'form-control' }

		.row
			.col-md-6
				%h3 Ingredients
				#ingredients
					= f.simple_fields_for :ingredients do |ingredient|
						= render 'ingredient_fields', f: ingredient
					.links
						= link_to_add_association 'Add Ingredient', f, :ingredients, class: "btn btn-default add-button"		

	= f.button :submit, class: "btn btn-primary"
```

Then, we need to create a partial that named `_ingredient_fields.html.haml`.      
Under `app/views/recipes/_ingredient_fields.html.haml`
```haml
.form-inline.clearfix
	.nested-fields
		= f.input :name, input_html: { class: 'form-input form-control' }
		= link_to_remove_association "Remove", f, class: "form-button btn btn-default"
```
![image](https://github.com/TimingJL/recipe_box/blob/master/pic/add_ingredients.jpeg)






To be continued...