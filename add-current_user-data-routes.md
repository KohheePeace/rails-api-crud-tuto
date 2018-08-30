# Chap14 Add routes for current\_user data

### Design current\_user REST API routes



{% embed data="{\"url\":\"https://stackoverflow.com/questions/36520372/designing-uri-for-current-logged-in-user-in-rest-applications\",\"type\":\"link\",\"title\":\"Designing URI for current logged in user in REST applications\",\"description\":\"I need a URI in my REST API to retrieve the current logged in user. Usually I use GET on resource with ID, but the client doesn\'t know the ID of the user.   I found the following solutions:  By user\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.sstatic.net/Sites/stackoverflow/img/apple-touch-icon.png?v=c78bd457575a\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://cdn.sstatic.net/Sites/stackoverflow/img/apple-touch-icon@2.png?v=73d79a89bded\",\"width\":316,\"height\":316,\"aspectRatio\":1}}" %}



### routes.rb

```ruby
resource :user do
  resources :posts, controller: 'user_posts', only: [:index]
end
```



{% code-tabs %}
{% code-tabs-item title="config/routes.rb" %}
```ruby
Rails.application.routes.draw do
  root to: 'pages#index'

  namespace :api, path: '', constraints: { subdomain: 'api' } do
    post '/auth/google_oauth2/callback', to: 'omniauth_callbacks#google_oauth2'
    get '/access_tokens/extend', to: 'access_tokens#extend'
    delete '/logout', to: 'access_tokens#destroy'

    resources :posts
    resource :user do
      resources :posts, controller: 'user_posts', only: [:index]
    end
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### controller

```text
rails g controller api/user_posts
```



{% code-tabs %}
{% code-tabs-item title="app/controllers/api/user\_posts\_controller.rb" %}
```ruby
class Api::UserPostsController < ApplicationController
  before_action :authenticate_with_token!, only: [:index]

  def index
    render json: current_user.posts
    authorize current_user.posts
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### References

{% embed data="{\"url\":\"https://stackoverflow.com/questions/14945754/rails-nested-resources-and-routing-how-to-break-up-controllers\",\"type\":\"link\",\"title\":\"Rails nested resources and routing - how to break up controllers?\",\"description\":\"I have the following models: Post Tag TaggedPost \(from which Post and Tag derive their associations by has\_many :through\) And I have the following routes.rb file:  resources :tags  resources :pos...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.sstatic.net/Sites/stackoverflow/img/apple-touch-icon.png?v=c78bd457575a\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://cdn.sstatic.net/Sites/stackoverflow/img/apple-touch-icon@2.png?v=73d79a89bded\",\"width\":316,\"height\":316,\"aspectRatio\":1}}" %}



### 



