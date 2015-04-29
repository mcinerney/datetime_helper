# Datetime Helper

A collection of useful utilities for projects that have to deal with dates, times, and timezones, with a focus on rails projects that enforce the use of Zulu Time.

### Current Status: Feature Complete

1. The `rspec` matcher `be_zulu_time` has been implemeted.
2. The `zulu_time` `ActiveModel` validator has been implemented.
2. `in_zulu_time` ActiveModel::Serializer helper method has been implemented.

#### Remaining to do

1. Code review, and finalisation of the module names, class names, and file names
2. Get `simplecov` working again

## TL;DR

Zulu Time is an ISO 8601 formatted string representing a `datetime` but in the time zone UTC+0. This makes it trivial for client applications to display correctly in their local, or other nominated time zones.

Enforcing Zulu Time across a range of projects requires a common approach to how you validate incoming strings, how you represent the data internally, how you serialise the data back out into strings, and how you test all that consistently and efficiently.

`Datetime Helper` provides that common approach.

## To use

Put this in your `Gemfile`

```ruby
gem 'westfield_datetime_helper', git: "http://github.com/westfieldlabs/datetime_helper.git"
```

### Using the `be_zulu_time` matcher in your `RSpec` tests

Put this in your `spec_helper.rb` or equivalent

```ruby
require 'westfield_datetime_helper/rspec'

RSpec.configure do |config|
  config.include WestfieldLabs::DatetimeHelper::Matchers
end
```

And put this in your `rspec` tests.

```ruby
it {expect(subject[:deleted_at]).to be_zulu_time}
```

### Validating `ActiveModel` fields to ensure they hold UTC+0 `date-time` data

Put this in your model for datetime fields that must be stored as UTC+0

First be sure you `require 'westfield_datetime_helper/active_model'`

Then your model class can add:

```ruby
include WestfieldLabs::DatetimeHelper::Validators

validates :updated_at, zulu_time: true
```

This will verify that a `Time` is supplied at `UTC+0`, or that a `DateTime` has `.zone == "+00:00"`.

You can also simply test a string to see if it is a Zulu Time string using

```ruby
WestfieldLabs::DatetimeHelper.is_zulu_time? "some_string"
```

### Enforcing `ActiveModel::Serializer`  Zulu Time string formats

First be sure you `require 'westfield_datetime_helper/active_model_serialisers'`

Then you can put this in your serialisers:

```ruby
extend WestfieldLabs::DatetimeHelper::Serialisers

in_zulu_time :updated_at
```

or if you have a bunch of 'em

```ruby
extend WestfieldLabs::DatetimeHelper::Serialisers

%w(updated_at deleted_at).each { |attribute| in_zulu_time attribute }
```

This will ensure that the serialised output is a proper Zulu Time formatted string.

## To build

```sh
bundle install
gem build westfield_datetime_helper.gemspec
```

## To test

```sh
bundle install
rake
```

The tests offer good insight into how to use these utilities.

## To contribute

Contributions are welcome.  See the [contribution instructions](contributing.md) for the preferred contribution process.
