---
title: Simple Request Sending through Rails
abbrlink: 5c7b6b12
date: 2013-09-11 19:14:44
tags:
---
```ruby
require 'net/http'
require 'uri'
require 'json'

def send_request(dep_dns, current_question, current_reply)
  uri_path = "/panel/ask/back_end/ask_requests/auth_request"
  uri = URI.parse("http://#{dep_dns}#{uri_path}")

  body = { 'auth_token' => '3kjlfksjDFJ' }

  http = Net::HTTP.new(uri.host, uri.port)
  request = Net::HTTP::Post.new(uri.request_uri)
  request.set_form_data(body)
  response = http.request(request)
  puts "Response #{response.code} #{response.message}: #{response.body}"
end
```

After then, we write a route for the request to take place, and build a controller action to send back the corresponding response.
