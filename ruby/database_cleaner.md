---
title: Database Cleaner
category: help
intro: Database cleaner RSpec setup
keywords:
  - Ruby
  - Rails
  - RSpec
  - Specs
  - Automated tests
  - Database
---

Basic setup for `Database cleaner` with `RSpec`.

### Database cleaner setup

#### `spec/support/database_cleaner.rb`

```ruby
RSpec.configure do |config|

  config.before(:suite) do
    DatabaseCleaner.clean_with(:truncation)
  end

  config.before(:each) do
    DatabaseCleaner.strategy = :transaction
  end

  config.before(:each, js: true) do
    DatabaseCleaner.strategy = :truncation
  end

  config.before(:each) do
    DatabaseCleaner.start
  end

  config.after(:each) do
    DatabaseCleaner.clean
  end

end
```

### Rspec setup

#### `spec/rails_helper.rb`

After `require 'rspec/rails'`

```ruby
# Load support files
Dir[Rails.root.join('spec', 'support', '**', '*.rb')]
  .sort
  .each { |f| require f }
```
{: .-wrap}

### References
{: .-intro}

- [Original Gist](https://gist.github.com/jsteiner/8362013) _jsteiner/database_cleaner.rb_
- [DatabaseCleaner GH](https://github.com/DatabaseCleaner/database_cleaner#rspec-example) _DatabaseCleaner/database_cleaner_
