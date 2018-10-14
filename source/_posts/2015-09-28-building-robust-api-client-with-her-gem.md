---
title: Building Robust API Client with Her Gem
abbrlink: 751b5e
date: 2015-09-28 20:26:28
tags:
---
The topic sounds a little awkward, but there's really a gem called `Her`

Here's the official documentation
http://www.her-rb.org/

### Installation
In your Gemfile, add
```ruby
gem "her"
gem "faraday", "~> 0.8.10"
```

I specified the `faraday` version to be locked at `~> 0.8.10` because there's something wrong the the newest version so far.

### Usage
```ruby
# config/initializers/her.rb
Her::API.setup url: "https://api.example.com" do |c|
  # Request
  c.use Faraday::Request::UrlEncoded

  # Response
  c.use Her::Middleware::DefaultParseJSON

  # Adapter
  c.use Faraday::Adapter::NetHttp
end
```

And then to add the ORM behavior to a class, you just have to include `Her::Model` in it:

```ruby
class User
  include Her::Model
end
```

After that, using Her is very similar to ActiveRecord:

```ruby
User.all
# GET "https://api.example.com/users" and return an array of User objects

User.find(1)
# GET "https://api.example.com/users/1" and return a User object

@user = User.create(fullname: "Tobias Fünke")
# POST "https://api.example.com/users" with `fullname=Tobias+Fünke` and return the saved User object

@user = User.new(fullname: "Tobias Fünke")
@user.occupation = "actor"
@user.save
# POST "https://api.example.com/users" with `fullname=Tobias+Fünke&occupation=actor` and return the saved User object

@user = User.find(1)
@user.fullname = "Lindsay Fünke"
@user.save
# PUT "https://api.example.com/users/1" with `fullname=Lindsay+Fünke` and return the updated User object
```

### Customization

For custom primary key:

```ruby
class User
  # ...
  primary_key :user_id # use user_id instead of id
end
```

For custom collection path:

```ruby
class User
  # ...
  collection_path "user_all" # "/user_all" instead of "/users"
end
```

For custom association:

```ruby
class User
  # ...
  has_many :comments, data_key: "comment_items", path: "comment_items"
  # "data key" represents the attribute in the user collection JSON for comments
  # "path" specifies the route for the resource ex."/users/:id/comment_items"
end
```
