---
title: Tips for Improve SQL Query Performance in Rails Using Counter Cache
date: 2014-04-03 20:06:44
tags:
---
I recently discovered that **COUNTER CACHE** is a great way to improve some SQL query performance across relational tables.

Since I have  **TOP LINKS** showing on my **SIMPLE REDDIT** web app, I encountered some issues to scale the top links performance with Rails. Later, I found that counter cache might be the solution

### Here's what my implementation in `pages_controller.rb` looks like
``` ruby
@popular_links = Link.includes(:category, :votes).top
```

I originally designed the vote model seperately which contains "up" vote and "down" vote attributes.

### Relate Link Model to fake votes attributes using `class_name`
``` ruby
has_many :up_votes, class_name: 'Vote', conditions: { up: true }
has_many :down_votes, class_name: 'Vote', conditions: { up: false }
```

### Compare the value from highest to lowest
``` ruby
def self.top
  self.all.sort do |a, b|
    (b.up_votes.length - b.down_votes.length) <=>
    (a.up_votes.length - a.down_votes.length)
  end.first(6)
end
```

The query at this stage is quite slow because it always takes up to 200ms to complete the page before counter cache is implemented. Therefore, I followed along with the [RailsCasts](http://railscasts.com/episodes/23-counter-cache-column) tutorial and tried to modify some of my code.

### Add migration to up and down votes count
``` ruby
add_column :links, :up_votes_count, :integer, default: 0

Link.reset_column_information
Link.all.each do |link|
  link.update_attribute :up_votes_count, link.up_votes.length
end
```
And do the same to down votes.

### Specify the cache column to Vote Model
``` ruby
  belongs_to :link, counter_cache: :up_votes_count
  belongs_to :link, counter_cache: :down_votes_count
```

Seems easy enough! Now I have my page completed under 30ms most of the time and I won't see lots of query showing up on my log screen. Anyway, I believe this is a great way for me to solve problems like this in the future.
