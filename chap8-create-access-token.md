# Chap8 Create Access Token

### Create Model

```text
rails g model AccessToken token_digest:string:index user:references expires_at:datetime
```

```text
rails db:migrate
```



### Edit User.rb

Add this

```text
has_many :access_tokens, dependent: :destroy
```

So, file looks now...

{% code-tabs %}
{% code-tabs-item title="models/user.rb" %}
```ruby
class User < ApplicationRecord
  validates :email, presence: true, uniqueness: true
  has_many :access_tokens, dependent: :destroy
  
  def self.create_with_google_omniauth(auth)
    create! do |user|
      user.google_uid = auth["uid"]
      user.email = auth["info"]["email"]
      user.image = auth["info"]["image"]
    end
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}





### Edit omniauth\_callbacks.rb

After create the user by google-omniauth, 

we need to ...

1. Create access token for the user
2. Render access token and useful info to client

{% code-tabs %}
{% code-tabs-item title="omniauth\_callbacks.rb" %}
```ruby
class Api::OmniauthCallbacksController < ApplicationController
  def google_oauth2
    auth = request.env['omniauth.auth']
    user = User.find_by_google_uid(auth['uid']) || User.create_with_google_omniauth(auth)
    # (1) generate access token for user
    access_token = AccessToken.generate_token(user)
    # (2) render access token and other info
    # It is useful to pass 'user' and 'expiresAt' in future client implementation
    render json: { user: user.as_json(only: [:id, :image]),
                   accessToken: access_token.token,
                   expiresAt: access_token.expires_at }, status: :ok
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### Add Model method

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
    # 4. save AccessToken in database by the above info
    AccessToken.create(token: token, token_digest: token_digest, expires_at: expires_at, user: user)
  end

  def self.new_token
    SecureRandom.urlsafe_base64
  end

  def self.digest_token(token)
    Digest::SHA256.hexdigest(token)
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}





### Conclusion

We can generate access token. In next we will learn how to restrict access by using this access token.



