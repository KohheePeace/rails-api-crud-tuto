# Chap10 current\_user and Post CRUD

Here we implement

current\_user and complete post crud actions.

```ruby
def current_user
  token = AccessToken.find_token(bearer_token)

  if token && !token.expired?
    @current_user ||= token.user
  end
end

def user_signed_in?
  current_user.present?
end
```

so...

{% code-tabs %}
{% code-tabs-item title="controllers/concerns/authable.rb" %}
```ruby
module Authable
  def current_user
    token = AccessToken.find_token(bearer_token)
    
    if token && !token.expired?
      @current_user ||= token.user
    end
  end
  
  def user_signed_in?
    current_user.present?
  end
  
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



### Edit posts\_controller.rb

Add `authenticate_with_token!` to :create, :update, :destroy

```text
before_action :authenticate_with_token!, only: [:create, :update, :destroy]
```



{% code-tabs %}
{% code-tabs-item title="posts\_controller.rb" %}
```ruby
class Api::PostsController < ApplicationController
  before_action :set_post, only: [:show, :update, :destroy]
  before_action :authenticate_with_token!, only: [:create, :update, :destroy]

  def index
    render json: Post.all
  end

  def show
    render json: @post
  end

  def create
    @post = current_user.posts.new(post_params)
    if @post.save
      render json: @post, status: :created
    else
      render json: @post.errors, status: :unprocessable_entity
    end
  end

  def update
    if @post.update(post_params)
      render json: @post, status: :ok
    else
      render json: @post.errors, status: :unprocessable_entity
    end
  end

  def destroy
    @post.destroy
    head :no_content
  end

  private
  def set_post
    @post = Post.find(params[:id])
  end

  def post_params
    params.require(:post).permit(:title, :content)
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Add user references to post

To use code like ...

```text
current_user.posts
```

We need to add user column to post model.

```text
rails g migration AddUserToPosts user:references
```

```text
rails db:migrate
```

In `user.rb` add ...

```text
has_many :posts, dependent: :destroy
```

so...

{% code-tabs %}
{% code-tabs-item title="models/user.rb" %}
```ruby
class User < ApplicationRecord
  validates :email, presence: true, uniqueness: true
  has_many :access_tokens, dependent: :destroy
  has_many :posts, dependent: :destroy

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

In `models/post.rb` add...

```text
belongs_to :user
```

so...

{% code-tabs %}
{% code-tabs-item title="models/post.rb" %}
```ruby
class Post < ApplicationRecord
  belongs_to :user
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### Finish!



### But ...

Only authenticate\_with\_token, _**user can edit others post.**_

So, in the next chapter we will add authorization.

