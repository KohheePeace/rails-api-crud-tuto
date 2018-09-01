# Delete extra tokens to avoid Too many client token to be generated!



{% embed data="{\"url\":\"https://github.com/lynndylanhurley/devise\_token\_auth/issues/210\",\"type\":\"link\",\"title\":\"Generating many client tokens · Issue \#210 · lynndylanhurley/devise\_token\_auth\",\"description\":\"I apologize for the cross-post, but I&\#39;m not sure if the problem I&\#39;m experiencing is related to this gem or it&\#39;s angular library counterpart \(or my improper implementation\), but I&\#39;ve...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars1.githubusercontent.com/u/1323414?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}



{% embed data="{\"url\":\"https://github.com/lynndylanhurley/devise\_token\_auth/issues/372\",\"type\":\"link\",\"title\":\"when does tokens field get cleared · Issue \#372 · lynndylanhurley/devise\_token\_auth\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars0.githubusercontent.com/u/3283141?s=400&v=4\",\"width\":420,\"height\":420,\"aspectRatio\":1}}" %}



When generate new accessToken, if tokens user have exceeds the limit, we delete the extra tokens.

{% code-tabs %}
{% code-tabs-item title="app/models/access\_token.rb" %}
```ruby
def self.maximum_tokens_per_user
  10
end

def self.delete_extra_tokens(user)
  maximum_tokens_per_user = self.maximum_tokens_per_user

  user.access_tokens
    .order(expires_at: :desc)
    .offset(maximum_tokens_per_user)
    .destroy_all
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}



Include `delete_extra_tokens` in `generate_token` 

```ruby
def self.generate_token(user)
  token = self.new_token
  token_digest = self.digest_token(token)
  expires_at = 14.days.from_now
  access_token = self.create(token: token, token_digest: token_digest, expires_at: expires_at, user: user)
  self.delete_extra_tokens(user) if user.access_tokens.length > self.maximum_tokens_per_user
  return access_token
end
```



So the final file is...

{% code-tabs %}
{% code-tabs-item title="app/models/access\_token.rb" %}
```ruby
class AccessToken < ApplicationRecord
  attr_accessor :token
  belongs_to :user

  def self.generate_token(user)
    token = self.new_token
    token_digest = self.digest_token(token)
    expires_at = 14.days.from_now
    access_token = self.create(token: token, token_digest: token_digest, expires_at: expires_at, user: user)
    self.delete_extra_tokens(user) if user.access_tokens.length > self.maximum_tokens_per_user
    return access_token
  end

  def self.maximum_tokens_per_user
    10
  end

  def self.delete_extra_tokens(user)
    maximum_tokens_per_user = self.maximum_tokens_per_user

    user.access_tokens
      .order(expires_at: :desc)
      .offset(maximum_tokens_per_user)
      .destroy_all
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

