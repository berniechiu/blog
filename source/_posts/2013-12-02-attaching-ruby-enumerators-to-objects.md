---
title: Attaching Ruby Enumerators to Objects
abbrlink: a34aea26
date: 2013-12-02 19:41:18
tags:
---
While reading "The Well-Grounded Rubyist", I found this topic quite interesting.

``` ruby
names = %w{ David Black Yukihiro Matsumoto }
e = names.enum_for(:select)
```

I could call `enum_for` on the object from which I want the enumerator to draw its iterations.

``` ruby
e.each { |n| n.include?('a') } #=> ["David", "Black", "Matsumoto"]
```

By calling upon `.each` method, I was able to allow the iterator to serve as a kind of front end to array's `select`.
