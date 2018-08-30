# Chap9 Authable Concerns

In the last chapter, we made access token.



### make Authable concerns

{% code-tabs %}
{% code-tabs-item title="controllers/concerns/authable.rb" %}
```ruby
module Authable
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}



{% embed data="{\"url\":\"https://stackoverflow.com/questions/18751027/whats-the-difference-between-a-controller-helper-and-a-concern\",\"type\":\"link\",\"title\":\"What\'s the difference between a controller helper and a concern?\",\"description\":\"Would concerns help me share logic for partials? I see Rails 4 has the \\\"concerns\\\" folder for controllers, but it\'s empty on app generation.  DHH wrote a blog on concerns \(but I think they are for the\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.sstatic.net/Sites/stackoverflow/img/apple-touch-icon.png?v=c78bd457575a\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://cdn.sstatic.net/Sites/stackoverflow/img/apple-touch-icon@2.png?v=73d79a89bded\",\"width\":316,\"height\":316,\"aspectRatio\":1}}" %}

If you're not familiar with concern, just think it is like helpers.





![](.gitbook/assets/rb-2018-08-11-06.44.46-001.jpg)

### Define `authenticate_with_token!`

{% code-tabs %}
{% code-tabs-item title="controllers/concerns/authable.rb" %}
```ruby
module Authable
  def user_have_valid_token?
    # Find token in database from bearer_token
    token = AccessToken.find_token(bearer_token)
    # 1. Check token exist
    # 2. Check token is not expired
    token && !token.expired?
  end

  def authenticate_with_token!
    render json: { errors: "Please Log in." }, status: :unauthorized unless user_have_valid_token?
  end

  # Find bearer token from request
  def bearer_token
    pattern = /^Bearer /
    header  = request.headers['Authorization']
    header.gsub(pattern, '') if header && header.match(pattern)
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### Add new methods in `access_token.rb`

`self.find_token(bearer_token)` and `expired?` 

{% code-tabs %}
{% code-tabs-item title="models/access\_token.rb" %}
```ruby
# == Schema Information
#
# Table name: access_tokens
#
#  id           :bigint(8)        not null, primary key
#  expires_at   :datetime
#  token_digest :string
#  created_at   :datetime         not null
#  updated_at   :datetime         not null
#  user_id      :bigint(8)
#
# Indexes
#
#  index_access_tokens_on_token_digest  (token_digest)
#  index_access_tokens_on_user_id       (user_id)
#
# Foreign Keys
#
#  fk_rails_...  (user_id => users.id)
#

class AccessToken < ApplicationRecord
  belongs_to :user
  # you can write code like below if you add 'attr_accessor'
  # even if there is no column 'token' in AccessToken database
  # ex. access_token = AccessToken.generate_token(user)
  # access_token.token
  attr_accessor :token

  def self.generate_token(user)
    # 1. create randam token
    token = self.new_token
    # 2. create digest of randam token
    token_digest = self.digest_token(token)
    # 3. set expires datetime
    expires_at = 14.days.from_now
    # 4. create AccessToken model by the above info
    AccessToken.create(token: token, token_digest: token_digest, expires_at: expires_at, user: user)
  end

  def self.new_token
    SecureRandom.urlsafe_base64
  end

  def self.digest_token(token)
    Digest::SHA256.hexdigest(token)
  end

  def self.find_token(bearer_token)
    # https://github.com/plataformatec/devise/blob/715192a7709a4c02127afb067e66230061b82cf2/lib/devise/token_generator.rb#L12
    token_digest = Digest::SHA256.hexdigest(bearer_token) if bearer_token
    AccessToken.find_by(token_digest: token_digest)
  end
  
  def expired?
    expires_at < Time.now
  end
end

```
{% endcode-tabs-item %}
{% endcode-tabs %}



{% embed data="{\"url\":\"https://stackoverflow.com/questions/44323531/how-to-get-bearer-token-passed-through-header-in-rails\",\"type\":\"link\",\"title\":\"How to get bearer token passed through header in rails?\",\"description\":\"In my rails application I\'m able to get the Token Authorization: Token token=\'aUthEnTicAtIonTokeN\' passed in header of request with  authenticate\_with\_http\_token do \|token, options\|  @auth\_token = ...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.sstatic.net/Sites/stackoverflow/img/apple-touch-icon.png?v=c78bd457575a\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://cdn.sstatic.net/Sites/stackoverflow/img/apple-touch-icon@2.png?v=73d79a89bded\",\"width\":316,\"height\":316,\"aspectRatio\":1}}" %}



### Include Authable in application\_controller.rb

{% code-tabs %}
{% code-tabs-item title="applicatoin\_controller.rb" %}
```ruby
class ApplicationController < ActionController::API
  include Authable
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### Check How it works!

Add below code in `posts_controller.rb`

```text
before_action :authenticate_with_token!, only: [:index]
```



{% code-tabs %}
{% code-tabs-item title="posts\_controller.rb" %}
```ruby
class Api::PostsController < ApplicationController
  before_action :authenticate_with_token!, only: [:index]
  def index
    render json: Post.all
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}



###  Copy `accessToken` in client by Console.log

To test Postman, we need to `console.log` `accessToken` and copy it

```text
console.log('this is result!',result)
```



{% code-tabs %}
{% code-tabs-item title="views/pages/index.html.erb" %}
```markup
...
<script>
function signInCallback(authResult) {
  if (authResult['code']) {
    console.log(authResult)
    // Hide the sign-in button now that the user is authorized, for example:
    $('#signinButton').attr('style', 'display: none');
    // Send the code to the server
    /* We need to implement backend endpoint here */
    $.ajax({
      type: 'POST',
      url: 'http://api.localhost:3000/auth/google_oauth2/callback',
      data: authResult,
      // Always include an `X-Requested-With` header in every AJAX request,
      // to protect against CSRF attacks.
      headers: {
        'X-Requested-With': 'XMLHttpRequest'
      },
      success: function(result) {
        // Handle or verify the server response.
        console.log('this is result!',result)
      },
    });
  } else {
    // There was an error.
  }
}
</script>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](.gitbook/assets/sukurnshotto-2018-08-12-125547.png)

**Copy the `accessToken`**



### Test with Postman

![](.gitbook/assets/sukurnshotto-2018-08-12-132545.png)

If you change the token

![](.gitbook/assets/sukurnshotto-2018-08-12-132629.png)

It renders errors message.

### Reference in this chapter

{% embed data="{\"url\":\"https://github.com/lynndylanhurley/devise\_token\_auth/blob/72976d8d3a0cd8f24578138f9edb7e7930fb75b2/lib/devise\_token\_auth/controllers/helpers.rb\",\"type\":\"link\",\"title\":\"lynndylanhurley/devise\_token\_auth\",\"description\":\"devise\_token\_auth - Token based authentication for Rails JSON APIs. Designed to work with jToker and ng-token-auth.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars0.githubusercontent.com/u/468037?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"http://apionrails.icalialabs.com/book/chapter\_five\",\"type\":\"link\",\"title\":\"APIs on Rails\",\"description\":\"The best way to build a REST API using Rails\",\"icon\":{\"type\":\"icon\",\"url\":\"http://apionrails.icalialabs.com/apple-touch-icon-precomposed.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://softcover.s3.amazonaws.com/1615/api\_on\_rails/images/cover-web.png\",\"width\":560,\"height\":840,\"aspectRatio\":1.5}}" %}

{% embed data="{\"url\":\"https://github.com/railstutorial/sample\_app\_rails\_4/blob/0cba8ffccf8b384d5cf7d62f8028a9e01b5c7d1f/app/helpers/sessions\_helper.rb\",\"type\":\"link\",\"title\":\"railstutorial/sample\_app\_rails\_4\",\"description\":\"sample\_app\_rails\_4 - The reference implementation of the sample app for the Ruby on Rails Tutorial \(Rails 4\)\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars1.githubusercontent.com/u/35562?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}



In clearance `authenticate_with_token!` is `require_login`

{% embed data="{\"url\":\"https://github.com/thoughtbot/clearance/blob/5cec3a8af844e3cbb5364a676f426725864ff8d0/lib/clearance/authorization.rb\",\"type\":\"link\",\"title\":\"thoughtbot/clearance\",\"description\":\"clearance - Rails authentication with email & password.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars1.githubusercontent.com/u/6183?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}

