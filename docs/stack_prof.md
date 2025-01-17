# Profiling with StackProf

[StackProf](https://github.com/tmm1/stackprof) is a sampling call-stack profiler for ruby.

## Instructions

Install `stackprof` gem (>= 0.2.9):

```ruby
# Gemfile
group :development, :test do
  gem "stackprof", ">= 0.2.9", require: false
end
```

StackProf profiler has 2 modes: `global` and `per-example`.

You can activate global profiling using the environment variable `TEST_STACK_PROF`:

```sh
TEST_STACK_PROF=1 bundle exec rake test

# or for RSpec
TEST_STACK_PROF=1 rspec ...
```

Or in your code:

```ruby
TestProf::StackProf.run
```

TestProf provides a built-in shared context for RSpec to profile examples individually:

```ruby
it "is doing heavy stuff", :sprof do
  # ...
end
```

**NOTE:** per-example profiling doesn't work when the global profiling is activated.

## Report formats

Stackprof provides a CLI tool to manipulate generated reports (e.g. convert to different formats).

By default, Test Prof shows you a command\* to generate an HTML report for analyzing flamegraphs, so you should run it yourself.

\* only if you're collecting _raw_ samples data, which is the default Test Prof behaviour.

Sometimes it's useful to have a JSON report (e.g. to use it with [speedscope](https://www.speedscope.app)), but `stackprof` only supports this in [`master`](https://github.com/tmm1/stackprof/pull/103).

That's why Test Prof has support for generating JSON reports from _raw_ dumps. For that, use `TEST_STACK_PROF_FORMAT=json` or configure the default format in your code:

```ruby
TestProf::StackProf.configure do |config|
  config.format = "json"
end
```

## Profiling application boot

The application boot time could also makes testing slower. Try to profile your boot process with StackProf using the following command:

```sh
TEST_STACK_PROF=boot rspec ./spec/some_spec.rb
```

**NOTE:** we recommend to analyze the boot time using flame graphs, that's why raw data collection is always on in `boot` mode.

## Configuration

You can change StackProf mode (which is `wall` by default) through `TEST_STACK_PROF_MODE` env variable.

See [stack_prof.rb](https://github.com/palkan/test-prof/tree/master/lib/test_prof/stack_prof.rb) for all available configuration options and their usage.
