# Zomies on Rails 

## Lection 1

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



























