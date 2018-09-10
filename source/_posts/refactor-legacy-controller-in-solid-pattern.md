---
title: Refactor Legacy Controller in a Good SOLID Pattern
date: 2018-09-10 08:51:18
tags: ruby
keywords: ruby,rails,design pattern
description:
---

### The Problem

Refactoring is hard, but I believe looking at the code that is hard to read is even poisonous to the team. Code smell happens all the time, and how we refactor it not only improve the readability but also provide a good sample for incoming juniors' career development in the company.

Let's looks that this piece of code:

```ruby
 module Api
   module Admin
     module Analytics
       class ProductsController < V1::ApiController
         def top_sold
           raise ParamsMissing, 'params missing' if params_missing
           raise InvalidDateInterval, 'range of days is not allowed' unless check_date_interval
           response = perform(
             merchant_id: current_merchant.id,
             url: analytics_params[:url]
             start_date: analytics_params[:start_date],
             end_date: analytics_params[:end_date]
           )
 
           render json: ::Analytics::FormatService.top_product_format(response['metrics']), status: :ok
         rescue RestClient::Exception => ex
           render json: JSON.parse(ex.response), status: :unprocessable_entity
         rescue => ex
           render json: { message: ex.message }, status: :unprocessable_entity
         end
         
         ...
         ...
         ...
       end
     end
   end
 end
end
```

Even the functions are separated, but let's refresh the responsibility of the Rails `controller`

```
It coordinates the interaction between the user, the views, and the model. The controller is also a home to a number of important ancillary services. It is responsible for routing external requests to internal actions.
```

Simple and elegant. However, what we did here kind of violates the first SOLID principles which is `Single Responsibility`.

Look at the line `6` to `7`, basically it's doing the job besides the controller routing but to validate errors. If we want to customize and even extend more validations, might end up all the private methods in controller and action in front of action definition.

Also, line `8` to `11` also comes across a request of a third party handler, even a `RestClient` or `HTTParty` request being put here looks out of place. If we want to extend more methods like `post` or `form` requests, definitely will bloat up the private functions being defined in the controller here.

Of course, line `13` to `18` looks out of place too. We have a decorator/serializer here but strongly depend on the single attribute of the response. And other responses seem not to fit in this decorator. What if we want to change the format when the endpoint change, this looks like a violation of `Open/Close Principle` for me.

Let's refresh the definition of the `Open/Close Principle`

```
software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification
```

### What I Did Here

Let's extract the request handler to its own module

```ruby
module Anaylytics
  class Request
    include Singleton
 
    BASE_URL = "#{SERVICES['anaylytics']['api_host']}".freeze
    URLS = {
      top_sale: 'top_sale'
    }.freeze
 
 
    class << self
      extend Forwardable
 
      def method_added(method)
        (class << self; self; end).def_delegator :instance, method
      end
    end
 
    def get(key, params: {})
      RestClient::Request.execute(
        method: :get,
        url: "#{BASE_URL}/#{URLS[key]}",
        headers: { params: params }
      )
    end
  end
end
```


