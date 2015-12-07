# Zombies on Rails 

## Level 1

 DB table can be accessed by class name as a table db -> tweets by class Tweets 
 returned value is a aobject and can be used to save/ update the row in DB 

```ruby

	t = Tweets.find(3) #  will return row id 3 

	t[:name]  = "test"  # or 
	t.name    = "test"  # same

	t.save 



	#simple db pattern


	t = Tweets.new 
	t.status  = "I <3 brains"
	t.save

	# can be used as 

	t = Tweet.new(
			status: "I <3 brains"
			zombies: "Jim"
		)
	t.save

	#The Recipe

	t = TableName.new(hash)
	t.save


	Tweet.create( status: "I <3 brains" , zombie: "jim")

	# The  Recipe
	TableName.create(hash)

```


 ###Read multiple data form database: 

 ```ruby
 	
 	Tweet.find(2)
 	Tweet.find(3,4,5,...)
 	Tweet.first  #  get the first item
 	Tweet.last   # get last
 	Tweet.all

 	Tweet.count
 	Tweet.order(:zombie)
 	Tweet.limit(10)
 	Tweet.where(zombie: "ash")
	
	# method chain 

	Tweet.where(zombie: "ash").order(:status).limit(10)


	#or 
	Tweet.where(zombie: "ash").first

 ```

 ### Update a Zombie

```ruby
t =  Tweet.find(2)
t.attributes = {
	status: "Can I munch your eyeballs",
	zombie: "EyeballChomeper"
}

t.save


# next

t =  Tweets.find(2)
t.update (
	
	status: "Can I munch your eyeballs",
	zombie: "EyeballChomeper"
)




```

### Delete a Zombie


```ruby

t = Tweet.find(2)
t.destroy

Tweet.find(2).destroy

Tweet.destroy_all




```



-----


#Level 2


### Models

 app/models/tweet.rb


```ruby
	
	class Tweet < ActiveRecord::Base 

```


`ActiveRecords::Base` is a class where the mapping is actually taking place 
so if you will run `t = Tweet.new.save` it will create an empty line in the db

In order to set mandatory field to be filled we can use class definitions

```ruby
	
	class Tweet < Active::Base
		vallidates_presence_of :status		
	end

```
now this `t = Tweet.new.save` will return false unless the mandatory field will be set

` t.errors.messages   and   t.errors[:status][0] `


### Validations

* validates_presence_of     :status
* validates_numericality_of :fingers
* validates_uniqueness_of   :toothmarks
* validates_confirmation_of :passwd 
* validates_c_acceptance_of :zombification 
* validates_length_of       :password, minimum:3 
* validates_format_of       :email , with /regex/i
* validates_inclusions_of   :age , in: 21..99
* validates_exclusion_of    :age, in: 0..21 , message: "Sorry you must be over 21" 

####ALternative syntax 

```ruby
		
		validates :status, presence: true	
		validates :status, length: {minimum: 3} 


		####### or #####

		validates :status,
							presence: true
							length: {minimum: 3}

```

## Relationships

Here is two tables with simple relationships 


#####tweets:

|id | status | zombie_id |
|---|:------:|:---------:|
|1  | aaaaaa |  1        | 
|2  | bbbbbb |  2        | 
|3  | cccccc |  1        | 


and 

#####zombies:
|id | name   | graveyard |
|---|:------:|:---------:|
|1  | Ash    |  New York | 
|2  | Bob    |  Boston   |
|3  | Alex   |  1        | 


So here is we can say that zombie have many tweets 

```ruby

	class Zombie < ActiveRecord::Base
		has_many :tweets

	end

```
and now we have to set the second part of this relationships

```ruby
class Tweet < ActiveRecord::Base
	belongs_to :zombie
end

```


### Live workaround 

```ruby 

	ash = Zombie.find(1)  ### it will return the ash  record 

	##  now ash can tweet 

	t = Tweet.create( status: "your eyelids taste like bacon, " , zombie: ash)

	## here rails will map all this behid the stage 

	ash.tweets.count 
	ash.tweets  ## will return all tweets by ash


	#### so the other side of the mapping 

	t.zombie ## will return the ash object 
	t.zombie.name will return "Ash"

```


-----


#Level 3 - View

> ERB is not Edible Rotting Bodies, is a Embedded Ruby


 The example of such view is simple view file ...
___/app/views/tweets/show.html.erb___

### The code:

```html
	<DOCTYPE html>
	<html>
	<head>
		<title> Rails For Zombies</title>
	</head>
		<body>
			<header>...</header>
			
			<% tweet = Tweet.find(1) %>

			<h1> <%= tweet.status %> </h1>
			<p> Posted by <%= tweet.zombie.name %> </p>

		</body>

	</html>

```

In order to avoid  the massive duplication of the code per each page 
we can move the HTML code to ___/app/views/layouts/appliation.html.erb___ 

```html
	<DOCTYPE html>
	<html>
	<head>
		<title> Rails For Zombies</title>
	</head>
		<body>
			<header>...</header>
			
			<%= yield %>

		</body>

	</html>

```


and the code to ___/app/views/tweets/show.html.erb___


```ruby

			<% tweet = Tweet.find(1) %>
			<h1> <%= tweet.status %> </h1>
			<p> Posted by <%= tweet.zombie.name %> </p>

```


### A link 

to use a link for some variable here is the helpers methods 

```ruby
	<%= link_to tweet.zombie.name , zombie_path(tweet.zombie) %>
```


alternate syntax 

```ruby
	<%= link_to tweet.zombie.name , tweet.zombie %>
```

here we can just send zombie instance to the link to and rails will know that we need to map this zomibe page to the link.
we also can add confirmation for this link `<%= link_to tweet.zombie.name , tweet.zombie,  confirm: "Are you sure?"  %>`

#### list the tweets in loop

```ruby
<h1></h1>
<table>
	
	<tr>
		<th>Status</th>
		<th>Zombie</th>
	</tr>
	<% tweets = Tweet.all %>

	<% if tweets.size == 0 %>
		<span>No tweets found</span>
	<% end > 

	<% tweets.all.each do |tweet | %>
		<tr>
			<td><%= link_to tweet.status, tweet %> </td>
			<td><%= link_to tweet.zombie.name, zombie %> </td>

			<td><%= link_to "Edit" , edit_tweet_path(tweet) %> </td>
			<td><%= link_to "Destroy", tweet, method: :delete %> </td>

		</tr>
	<% end %>	


</table>

```

So now we can see the whole picture :

|Action         | Code        | The URL   |
|---------------|:-----------:|:---------:|
|List all tweets| tweets_path | /tweets   | 



|Action          | Code                   | The URL          |
|----------------|:----------------------:|:----------------:|
|Show a tweet    | tweet                  | /tweets/1        | 
|edit a tweet    | edit_tweet_path(tweet) | /tweets/1/edit   | 
|delete a tweet  | tweet, method: :delete | /tweets/1/       | 



-----


#Level 4 - Controllers


 The control is where you control all your app
the call for the tweets shouldn't be in a view 
this is where a controller can make sense 

```ruby
<h1></h1>
<table>
	
	<tr>
		<th>Status</th>
		<th>Zombie</th>
	</tr>
	<% tweets = Tweet.all %>

	<% if tweets.size == 0 %>
		<span>No tweets found</span>
	<% end > 

	<% tweets.all.each do |tweet | %>
		<tr>
			<td><%= link_to tweet.status, tweet %> </td>
			<td><%= link_to tweet.zombie.name, zombie %> </td>

			<td><%= link_to "Edit" , edit_tweet_path(tweet) %> </td>
			<td><%= link_to "Destroy", tweet, method: :delete %> </td>

		</tr>
	<% end %>	


</table>

```

 

#### Request: /tweets/1

___/app/controllers/tweets_controllers.rb___

```ruby

	class TweetsController < ApplicationController
		
		def show
			@tweet = Tweets.find(1)
		end	
	end

```


___/app/view/tweets/show.html.erb___

```ruby

	<h1> <%= @tweet.status %> </h1>
	<p> Posted by <%= @tweet.zombie.name %> </p>

```


> problem:

	but what is we have to render the differed page to show 
	the results for example status.html.erb?
	here is an example:



___/app/controllers/tweets_controllers.rb___

```ruby

	class TweetsController < ApplicationController
		
		def show
			@tweet = Tweets.find(1)
			render action: 'status'
		end	
	end

```


___/app/view/tweets/status.html.erb___

```ruby

	<h1> <%= @tweet.status %> </h1>
	<p> Posted by <%= @tweet.zombie.name %> </p>

```

In order to access all parameters passed by the server 
we can do it by using the `params hash` value 

` params =  { id : '1' }`


so now we can get the proper tweet 

```ruby

	class TweetsController < ApplicationController
		
		def show
			@tweet = Tweets.find(params[:id])
			render action: 'status'
		end	
	end

```

use secure value only by :


```ruby

	@tweet = Tweet.create( params.require(:tweet).permit(:status) )  ### or 
	@tweet = Tweet.create( params.require(:tweet).permit([:status, :location]) )
	### instead of 
	@tweet = Tweet.create( params[:tweet][:status] )

```


##JSON

Some time for API / REST we have to support different formats 
in our case we will use JSON for this example 


```ruby

	class TweetsController < ApplicationController
		def show
			@tweet = Tweets.find(params[:id])
			respond_to do | format |
				format.html  ### show.html.erb
				format.json { render json: @tweet }
				format.xml  { render xml: @tweet }
			end	
		end
	end

```

## Controller Actions

```ruby

	class TweetsController < ApplicationController

		def index   # List all tweet
				
		def show    # Show a single tweet

		def new     # Show a edit tweet

		def edit    # Show an edit tweet form

		def create  # create a new tweet

		def update  # create a new tweet
		
		def destroy # create a new tweet


	end

```



## Redirect and Flash


* session             Works like per user hash
* flash[:notice]      To send messages to the user
* redirect_to <path>  To redirect the request

```ruby

	class TweetsController < ApplicationController
		def edit
			@tweet = Tweets.find(params[:id])
			
			if session[:zombie_id] != @tweet.zombie.id
				flash[:notice] = "Sorry you can't see/ edit it "
				redirect_to(tweets_path)
			end
		end
	end

```

or alternate recipe: 


```ruby

				redirect_to(tweets_path , notice: "Sorry you can't see/ edit it ")

```


Now we have to  specify the view where the notice will go 


```ruby

	<h1> <%= @tweet.status %> </h1>
	<p> Posted by <%= @tweet.zombie.name %> </p>

	<% if flash[:notice] %>

	<% end %>


```

Now we can move the duplicate code to get tweets and auth
to a function and use it according to the D.R.Y


```ruby

	class TweetsController < ApplicationController

		before_action :get_tweet  , only: [:edit, :update, :destroy]
		before_action :check_outh , only: [:edit, :update, :destroy]

		def check_auth
			if session[:zombie_id] != @tweet.zombie_id
			redirect_to(tweets_path , notice: "Sorry you can't see/ edit it ")
		def

		def get_tweet 
			@tweets = Tweet.find(params[:id])
		end

		def edit
			
		end

		def update
			
		end

		def destroy
			
		end
	end

```







