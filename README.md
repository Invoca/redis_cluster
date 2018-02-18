# RedisCluster

![travis ci](https://travis-ci.org/zhchsf/redis_cluster.svg?branch=master)

First see: [https://redis.io/topics/cluster-tutorial](https://redis.io/topics/cluster-tutorial)

RedisCluster for ruby is rewrited from [https://github.com/antirez/redis-rb-cluster](https://github.com/antirez/redis-rb-cluster)

Support: Ruby 2.0+

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'redis_cluster'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install redis_cluster

## Usage

First you need to configure redis cluster with some nodes! Please see: [https://redis.io/topics/cluster-tutorial](https://redis.io/topics/cluster-tutorial)

```ruby
# Don't need all, gem can auto detect all nodes, and process failover if some master nodes down
hosts = [{host: '127.0.0.1', port: 7000}, {host: '127.0.0.1', port: 7001}]
rs = RedisCluster.new hosts
rs.set "test", 1
rs.get "test"
```

At development environment with single redis node, you can set hosts a hash value: {host: 'xx', port: 6379}.

If masterauth & requirepass configed, you can initialize below:
```ruby
RedisCluster.new hosts, password: 'password'
```

now support keys command with scanning all nodes:
```ruby
rs.keys 'test*'
```

limited support commands: pipelined, multi
```ruby
# Only support pipeline commands to one redis node once
# You must ensure keys at one slot: use same key or hash tags
# If you don't, not raise any errors now
rs.pipelined do
  rs.set "{foo}one", 1
  rs.set "{foo}two", 2
end
```

script, eval, evalsha
```ruby
# script commands will run on all nodes
rs.script :load, "return redis.call('get', KEYS[1])"
rs.script :exists, '4e6d8fc8bb01276962cce5371fa795a7763657ae'
rs.script :flush

# eval/evalsha must executed at one node with hash tag keys in same slot
# and must use KEYS to fetch keys in lua script
rs.eval "return redis.call('get', KEYS[1]) + ARGV[1]", [:test], [3]
rs.evalsha '727fc2fb7c0f11ec134d998654e3dadaacf31a97', [:test], [5]

# if lua script don't depend on any keys and argvs, you also need execute with a key
rs.eval "return 'hello redis!'", [:foo]
```

## Benchmark test

```ruby
Benchmark.bm do |x|
  x.report do
    1.upto(100_000).each do |i|
      redis.set "test#{i}", i
    end
  end
  x.report do
    1.upto(100_000).each do |i|
      redis.get "test#{i}"
    end
  end
  x.report do
    1.upto(100_000).each do |i|
      redis.del "test#{i}"
    end
  end
end
```


## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/zhchsf/redis_cluster. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.


## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

