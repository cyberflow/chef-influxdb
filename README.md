# InfluxDB
Chef cookbook to install and configure InfluxDB.

## Usage
To install InfluxDB and place its configuration file, include the
`influxdb::default` recipe in your node's `run_list`. The `default` recipe
installs the necessary dependencies to use the custom resources defined in this
cookbook.

For rendering the config set the parameter under
`node['influxdb']['config']`

`default['influxdb']['config']['MyParameter'] = 'val'`

## Resources
The following gems are used by the custom resources and are installed by the
`default` recipe:

 - [InfluxDB gem](https://github.com/influxdb/influxdb-ruby)
 - [TOML](https://github.com/mojombo/toml)

This cookbook ships with five custom resources for managing the configuration
file, users, databases, cluster admins, and retention policies:

### influxdb_config
This resource writes a configuration file for InfluxDB based on the passed
configuration hash:

```ruby
influxdb_config node['influxdb']['config_file_path'] do
  config node['influxdb']['config']
  action :create
end
```

This resource is used by the `default` recipe to place the configuration
defined in `node['influxdb']['config']`.

### influxdb\_database
Configures an InfluxDB database.

```ruby
influxdb_database 'my_db' do
  action :create
end
```

### influxdb\_user
This resources configures a user to interact with InfluxDB databases.

```ruby
influxdb_user 'user' do
  password 'changeme'
  databases ['my_db']
  action :create
end
```

### influxdb\_admin
This resources configures a cluster admin to interact with InfluxDB.

```ruby
influxdb_admin 'admin' do
  password 'changeme'
  action :create
end
```

### influxdb\_retention\_policy
This resources configures a retention policy on a given database. The name
attribute is not used, the database and policy name provide the unique names
used by InfluxDB.

```ruby
influxdb_retention_policy "foodb default retention policy" do
  policy_name 'default'
  database 'foodb'
  duration '1w'
  replication 1
  action :create
end
```

## Client Libraries
Right now, this cookbook only supports the Ruby and CLI client libraries so as
not to add too many dependencies. That might change in the near future. By
default both flavors are disabled. Enable e.g. Ruby via:

`node.default['influxdb']['client']['ruby']['enable'] = true`

Finally include the `influxdb::client` in your node's `run_list` to install the
clients.

## Tests

To run tests, install all dependencies with [bundler](http://bundler.io/):

    bundle install

Then to run tests:

    rake # Quick tests only (rubocop + minitest)
    rake test:complete # All tests (rubocop + minitest + kitchen)

## Author and License
Simple Finance <ops@simple.com>

Apache License, Version 2.0
