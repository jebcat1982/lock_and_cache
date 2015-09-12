# LockAndCache

[![Build Status](https://travis-ci.org/seamusabshere/lock_and_cache.svg?branch=master)](https://travis-ci.org/seamusabshere/lock_and_cache)
[![Code Climate](https://codeclimate.com/github/seamusabshere/lock_and_cache/badges/gpa.svg)](https://codeclimate.com/github/seamusabshere/lock_and_cache)
[![Dependency Status](https://gemnasium.com/seamusabshere/lock_and_cache.svg)](https://gemnasium.com/seamusabshere/lock_and_cache)
[![Gem Version](https://badge.fury.io/rb/lock_and_cache.svg)](http://badge.fury.io/rb/lock_and_cache)
[![Security](https://hakiri.io/github/seamusabshere/lock_and_cache/master.svg)](https://hakiri.io/github/seamusabshere/lock_and_cache/master)
[![Inline docs](http://inch-ci.org/github/seamusabshere/lock_and_cache.svg?branch=master)](http://inch-ci.org/github/seamusabshere/lock_and_cache)

Lock and cache using redis!

## Sponsor

<p><a href="http://faraday.io"><img src="https://s3.amazonaws.com/photos.angel.co/startups/i/175701-a63ebd1b56a401e905963c64958204d4-medium_jpg.jpg" alt="Faraday logo"/></a></p>

We use [`lock_and_cache`](https://rubygems.org/gems/lock_and_cache) for [big data-driven marketing at Faraday](http://faraday.io).

## Theory

`lock_and_cache`...

1. returns cached value if found
2. acquires a lock
3. returns cached value if found (just in case it was calculated while we were waiting for a lock)
4. calculates and caches the value
5. releases the lock
6. returns the value

As you can see, most caching libraries only take care of (1) and (4).

## Practice

### Locking (antirez's Redlock)

Based on [antirez's Redlock algorithm](http://redis.io/topics/distlock).

Above and beyond Redlock, a 2-second heartbeat is used that will clear the lock if a process is killed. This is implemented using lock extensions.

```ruby
LockAndCache.storage = Redis.new
```

It will use this redis for both locking and storing cached values.

### Caching (block inside of a method)

(be sure to set up storage as above)

You put a block inside of a method:

```ruby
class Blog
  def click(arg1, arg2)
    lock_and_cache(arg1, arg2, expires: 5) do
      # do the work
    end
  end
end
```

The key will be `{ Blog, :click, $id, $arg1, $arg2 }`. In other words, it auto-detects the class, method, object id ... and you add other args if you want.

You can change the object id easily:

```ruby
class Blog
  # [...]
  # if you don't define this, it will try to call #id
  def lock_and_cache_key
    [author, title]
  end
end
```

## Tunables

* `LockAndCache.storage=[redis]`
* `ENV['LOCK_AND_CACHE_DEBUG']='true'` if you want some debugging output on `$stderr`

## Few dependencies

* [activesupport](https://rubygems.org/gems/activesupport) (come on, it's the bomb)
* [redis](https://github.com/redis/redis-rb)
* [redlock](https://github.com/leandromoreira/redlock-rb)

## Contributing

1. Fork it ( https://github.com/[my-github-username]/lock_and_cache/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request

# Copyright 

Copyright 2015 Seamus Abshere
