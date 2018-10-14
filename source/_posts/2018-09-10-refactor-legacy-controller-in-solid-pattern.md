---
title: Refactor Legacy Controller in a Good SOLID Pattern
tags: ruby
keywords: 'ruby,rails,design pattern'
abbrlink: 4f0bed1f
date: 2018-09-10 08:51:18
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

Let's first extract the request handler to its own module

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

Looks good, right? Now the request has its own handler and we can simply define methods like `GET`, `POST` of our own and extend easily.

And the singleton class with `method_added` being delegated, allows us not to write `.instance` all the time but to have the property of instance like class. This is a better thread safe management and allow us to extend some `ActiveSupport` methods that are specifically for instance methods.

Now let's define our `ErrorsHandler`

```ruby
module Anaylytics
  class Request
    module ErrorsHandler
      include AcctiveSupport::Concern
      include ActiveSupport::Rescuable

      included do
        rescue_from RequestError, with: :deny_access
      end

      protected

      class RequestError < StandardError
		 ...
		 ...
		 ...
      end

      private

      def deny_access
        ...
        ...
      end
    end
  end
end
```

Now we have customized errors and rescuer being defined in a single class. And adopt the use of few `ActiveSupport` modules to make it even cleaner like `rescue_from` allow us to define rescue function to its own method name.

How about `ParamsMissing` and `DateInvalid` in the original controller action? Seems not being placed here, and that's right! The logic sounds more like a model to me and I think should deserve to have its own validator here.

```ruby
module Analytics
  class Request
    class ParamsValidator
      include ActiveModel::Validations
      attr_accessor :start_date, :end_date

      INTERVAL = 3.months.freeze

      validates :start_date, presence: true
      validates :end_date, presence: true
      validate :belongs_to_interval

      def initialize(params = {})
        ...
      end

      def belongs_to_interval
        ...
      end

      def validate!
        raise ValidationError.new(errors.full_messages) if invalid?
        true
      end

      class ValidationError < StandardError; end
    end
  end

```

I sometimes find `ActiveModel::Validations` is very useful when we want to define something that's not actually in current scope of Rails database model. And we can still adopt the use the simple and elegant validation solution to its own class.

And last we see a format service that is running wild and not unified for each response in the original action. Let's rename it as presenter to delegate the response object.


```ruby
module Analytics
  module ResponsePresenters
    class Base
      attr_reader :object, :raw

      def initialize(object)
        @object = JSON.parse(object)
        @raw    = object
      end

      def as_json(*)
        ...
      end

      private

      ...
      ...
    end

    class OtherPresenter < Base
      ...
      ...
    end
  end
end
```

`as_json` allows the object in `render json:` to be automatically called and transform to JSON string. It's an example of `Liskov Subsitution Principle` or Duck Typing technique since we want the response-like object to be act like the `response` resulting from `RestClient`

Let's look at our refreshed request handler

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

    def get(key, params: {}, presenter: Analytics::ResponsePresenters::Base)
      # Added ParamsValidator
      ParamsValidator.new(params).validate!
      response = RestClient::Request.execute(
        method: :get,
        url: "#{BASE_URL}/#{URLS[key]}",
        headers: { params: params }
      )
      # Dependencies inject
      presenter.new(response)
    end
  end
end

```

Since we do not need all the presenters at once, and probably only need to present/decorate the response at each request. The `Open/Close Principle` is quite suitable here which gives us the idea to inject the module class we want.

Finally, our end result controller action will be like

```ruby
...
...
include Analytics::Request::ErrorsHandler

def top_sale
  response = Anaylytics::Request.get(:top_sale, params: {
    merchant_id: current_merchant.id,
    start_date: analytics_params[:start_date],
    end_date: analytics_params[:end_date]
  })

  render json: response, status: response.code
end
...
...

```

I think this example demestrate some of the SOLID principles and design patterns we can use in daily basis. Thanks for reading!


