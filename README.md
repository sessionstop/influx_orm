# InfluxOrm

A simple influxdb orm for ruby, base [influxdb-ruby](https://github.com/influxdata/influxdb-ruby)

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'influx_orm'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install influx_orm

## Usage

### Init

Default setup

```
InfluxORM.setup(
  database: 'xyz'
)
```

### Define MEASUREMENTS

```
class Memory
  include InfluxORM

  influx_tag :host
  influx_tag :region
  influx_val :free
  influx_val :used
end
```

### Write

```
Memory.insert(host: 'A', region: 'US', free: 1234, used: 2234)

Memory.import([
  {host: 'A', region: 'US', free: 1234, used: 2234, timestamp: 1234567890},
  {host: 'A', region: 'US', free: 1244, used: 2224, timestamp: 1234567900},
  {host: 'B', region: 'US', free: 234, used: 3234}
])
```

### Query

```
Memory.count # => 4
Memory.where(host: 'A').count # => 1
Memory.select('mean(*)') \
  .where(host: 'A', time: {gte: Time.now - 10.day, lte: Time.now - 1.day}) \
  .group_by('time(1m) fill(0)').result

Memory.where(host: 'B').limit(10).result
Memory.where("host = 'a' AND time > now() - 1d")
```

Support query methods

* `select`: `select('mean(*)')`, `select({mean: 'tag_name', sum: 'tag_name'})`
* `where`: `where('tag = \'value\'')`, `where(tag: 'value', time: {gt: Time.now - 1.day})`
* `group_by`: `group_by('time(1m) fill(0)')` eql `group_by_time(1m).fill(0)` 
* `group_by_time`: `group_by_time('1m')`
* `fill`: `fill(0)`
* `limit`: `limit(1)`
* `slimit`: `slimit(1)`
* `offset`: `offset(1)`
* `soffset`: `soffset(1)`


## Structure

```
-----------------
|   InfluxORM   |
|----- has -----|
|    Config     |
-----------------

------------------
|     Model      |
|--- included ---|
|   Connection   |
|     Query      |
|   Attributes   |
------------------
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/[USERNAME]/influx_orm.


## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).
