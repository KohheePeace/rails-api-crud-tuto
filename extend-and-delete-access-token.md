# Chap12 Extend and Delete access token

### Overview of this chapter

In current application, user token **only last "14days"**

This means that _**user must** **re-login in 14days**_.

It is bad for User experience.

So...

Implement the feature to **extend** the current access token.

Please check this topics in below references.



And...

We implement **logout feature**

### access\_tokens\_controller.rb

```text
rails g controller api/access_tokens
```



{% code-tabs %}
{% code-tabs-item title="controllers/api/access\_tokens\_controller.rb" %}
```ruby
class Api::AccessTokensController < ApplicationController
  # Add this line for only logged in user can execute these actions
  before_action :authenticate_with_token!

  def extend
    access_token = AccessToken.find_token(bearer_token)

    # expired? is already checked in authenticate_with_token but for safety...
    if !access_token.expired?
      access_token.update(expires_at: 14.days.from_now)
      render json: { expiresAt: access_token.expires_at}
    end
  end

  def destroy #(=> log_out)
    access_token = AccessToken.find_token(bearer_token)
    access_token.destroy
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### routes.rb

Add routes for these actions

```ruby
put '/access_tokens/extend', to: 'access_tokens#extend'
delete '/logout', to: 'access_tokens#destroy'
```

So...

{% code-tabs %}
{% code-tabs-item title="config/routes.rb" %}
```ruby
Rails.application.routes.draw do
  root to: 'pages#index'

  namespace :api, path: '', constraints: { subdomain: 'api' } do
    post '/auth/google_oauth2/callback', to: 'omniauth_callbacks#google_oauth2'
    put '/access_tokens/extend', to: 'access_tokens#extend'
    delete '/logout', to: 'access_tokens#destroy'

    resources :posts
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}





### References

{% embed url="https://github.com/auth0/node-jsonwebtoken/issues/316" %}

{% embed url="https://stackoverflow.com/questions/26739167/jwt-json-web-token-automatic-prolongation-of-expiration" %}

