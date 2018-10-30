# Chap15 Security for production

### Security

Add this later, something not working well if you didn't enable ssl in heroku.

{% code-tabs %}
{% code-tabs-item title="config/environments/production.rb" %}
```ruby
config.force_ssl = true
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### Rack-Attack

{% embed url="https://github.com/kickstarter/rack-attack" %}

```text
gem 'rack-attack'
```



{% code-tabs %}
{% code-tabs-item title="config/initializers/rack-attack.rb" %}
```ruby
class Rack::Attack
  ...
  # Key: "rack::attack:#{Time.now.to_i/:period}:req/ip:#{req.ip}"
  throttle('req/ip', limit: 300, period: 5.minutes) do |req|
    req.ip # unless req.path.start_with?('/assets')
  end
  ...
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

