# Ruby Timeouts

An unresponsive service is worse than a down one. It can tie up your entire system if not handled properly. **Any network request should have a timeout.**

Here’s how to add timeouts to common Ruby gems. **[All have been tested](test)**. The default is no timeout, unless otherwise specified. Enjoy!

## Categories

Data Stores

- [activerecord](#activerecord)
- [pg](#pg)
- [dalli](#dalli)
- [redis](#redis)
- [elasticsearch](#elasticsearch)
- [searchkick](#searchkick)

HTTP Clients

- [http](#http)
- [rest-client](#rest-client)
- [curb](#curb)
- [typhoeus](#typhoeus)

Web Servers

- [unicorn](#unicorn)
- [puma](#puma)

Rack Middleware

- [slowpoke](#slowpoke)
- [rack-timeout](#rack-timeout)

## Data Stores

### activerecord

For the `postgres` adapter

```ruby
ActiveRecord::Base.establish_connection host: host, connect_timeout: 1
```

or in `config/database.yml`

```yaml
production:
  connect_timeout: 1
```

Raises `PG::ConnectionBad`.

### pg

```ruby
PG.connect(host: host, connect_timeout: 1)
```

Raises `PG::ConnectionBad`.

### dalli

```ruby
Dalli::Client.new(host, socket_timeout: 1)
```

Default: 0.5s

Raises `Dalli::RingError`.

### redis

```ruby
Redis.new(host: host, connect_timeout: 1)
```

Raises `Redis::CannotConnectError`.

### elasticsearch

```ruby
Elasticsearch::Client.new(host: host, transport_options: {request: {timeout: 1}})
```

Raises `Faraday::ConnectionFailed`.

### searchkick

```ruby
Searchkick.timeout = 1
```

Raises `Faraday::ConnectionFailed`.

## HTTP Clients

### http

```ruby
HTTP.timeout(:per_operation, connect: 1).get(url)
```

Raises `HTTP::TimeoutError`.

### rest-client

```ruby
RestClient::Request.execute(method: :get, url: url, open_timeout: 1)
```

Raises `RestClient::RequestTimeout`.

### curb

```ruby
curl = Curl::Easy.new(url)
curl.connect_timeout = 1
curl.perform
```

Raises `Curl::Err::TimeoutError`.

### typhoeus

```ruby
response = Typhoeus.get(url, connecttimeout: 1)
```

No error is raised. Check for a timeout with:

```ruby
response.timed_out?
```

## Web Servers

### unicorn

```ruby
# config/unicorn.rb
timeout 15
```

It’s recommended to use this in addition to [Rack middleware](#rack-middleware).

### puma

There’s [no timeout option](https://github.com/puma/puma/issues/160). Use [Rack middleware](#rack-middleware) instead.

## Rack Middleware

### slowpoke

```ruby
Slowpoke.timeout = 5
```

Default: 15s

### rack-timeout

```ruby
Rack::Timeout.timeout = 5
```

Default: 15s

## Don’t see a library you use?

[Let us know](https://github.com/ankane/ruby-timeouts/issues/new). Even better, [create a pull request](https://github.com/ankane/ruby-timeouts/pull_requests/new) for it.