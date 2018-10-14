---
title: Comparing the Access of Ruby Instance Variables
abbrlink: 61f73bee
date: 2013-11-17 19:37:12
tags:
---
Sometimes I get a little confused about the difference between accessing an instance attribute via `self.attribute` and by `@attribute`, so one day I look up the [stackoverflow](http://stackoverflow.com/questions/4639271/directly-accessing-an-instance-variable-vs-using-an-accessor-method) to clear up my thoughts. The answers below are some good tips on the subject based on the site.

## Here's What the Best Answer Would Suggest

`self.attribute` calls the method attribute.
`self.attribute = value` calls the method `attribute=` with the argument `value`.
`@attribute` and `@attribute = value` get/set the value of the instance variable @attribute.

So basically they're two entirely different things.

However if you call `attr_accessor :attribute` it defines the method attribute to return `@attribute` and the method `attribute=(value)` to set `@attribute = value`. So in that case, there is no difference.

## The Comment is also Helpful

Note that it is generally recommended to use `self`. (unless you're writing the getter/setter method) even if you currently have attr_accessor. This protects you from additional refactor work and bugs if you later change the accessor method(s) to do more than just get/set the instance variable. (Or if someone else patches or subclasses your work.)
