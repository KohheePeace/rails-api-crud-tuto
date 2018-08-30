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

{% embed data="{\"url\":\"https://github.com/kickstarter/rack-attack\",\"type\":\"link\",\"title\":\"kickstarter/rack-attack\",\"description\":\"rack-attack - Rack middleware for blocking & throttling\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars1.githubusercontent.com/u/120676?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}

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

