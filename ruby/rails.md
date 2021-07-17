---
title: Rails
category: Rails
type: personal
---

# Rails

## Configuration

### Paths

```ruby
Rails.configuration.eager_load_paths
Rails.configuration.autoload_paths
```

## Intializers

### List initializers

```ruby
Rails.application.initializers.to_a.map { |el| el.name }
```

### Find initializer section

```ruby
initializer_name = :test
puts Rails::Application::Bootstrap.initializers_for(Rails.application).map(&:name).include?(initializer_name)
puts Rails::Application.initializers_for(Rails.application).map(&:name).include?(initializer_name)
puts Rails::Application::Finisher.initializers_for(Rails.application).map(&:name).include?(initializer_name)
```

- https://dev.to/st0012/how-to-replace-a-rails-initializer-30n0

