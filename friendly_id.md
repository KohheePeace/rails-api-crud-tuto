# Chap13 Friendly\_id for Post

{% embed url="https://github.com/norman/friendly\_id" %}



```text
gem 'friendly_id', '~> 5.2.0'
```



```text
bundle
```



```text
rails generate friendly_id
```

```text
Running via Spring preloader in process 50850
      create  db/migrate/20180816142039_create_friendly_id_slugs.rb
      create  config/initializers/friendly_id.rb
```



```text
rails db:migrate
```



### User title to generate friendly url slug

So...

```text
  extend FriendlyId
  friendly_id :title, use: :slugged
```

So... `models/post.rb` looks

{% code-tabs %}
{% code-tabs-item title="models/post.rb" %}
```ruby
class Post < ApplicationRecord
  extend FriendlyId
  friendly_id :title, use: :slugged

  belongs_to :user
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}





### Change url slug when title changed

```ruby
def should_generate_new_friendly_id?
    title_changed? || super
end
```

So, `models/post.rb` looks

{% code-tabs %}
{% code-tabs-item title="models/post.rb" %}
```ruby
class Post < ApplicationRecord
  extend FriendlyId
  friendly_id :title, use: :slugged

  belongs_to :user
  
  def should_generate_new_friendly_id?
    title_changed? || super
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}





> **Deciding When to Generate New Slugs**
>
> As of FriendlyId 5.0, slugs are only generated when the `slug` field is nil. If you want a slug to be regenerated,set the slug field to nil:
>
> ```text
> restaurant.friendly_id # joes-diner
> restaurant.name = "The Plaza Diner"
> restaurant.save!
> restaurant.friendly_id # joes-diner
> restaurant.slug = nil
> restaurant.save!
> restaurant.friendly_id # the-plaza-diner
> ```
>
> You can also override the [FriendlyId::Slugged\#should\_generate\_new\_friendly\_id?](http://norman.github.io/friendly_id/FriendlyId/Slugged.html#should_generate_new_friendly_id%3F-instance_method) method, which lets you control exactly when new friendly ids are set:
>
> ```text
> class Post < ActiveRecord::Base
>   extend FriendlyId
>   friendly_id :title, :use => :slugged
>
>   def should_generate_new_friendly_id?
>     title_changed?
>   end
> end
> ```



### Database migration

```text
rails g migration add_slug_to_posts slug:string:uniq
```

```ruby
class AddSlugToPosts < ActiveRecord::Migration[5.2]
  def change
    add_column :posts, :slug, :string
    add_index :posts, :slug, unique: true
  end
end
```



### controller

replace `Post.find` by `Post.friendly.find`

{% code-tabs %}
{% code-tabs-item title="posts\_controller.rb" %}
```ruby
private
  def set_post
    @post = Post.friendly.find(params[:id])
    authorize @post
  end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Update existing Post to generate slug

```ruby
rails c
# Delete first post because there is no user_id
# It throws error for belongs_to :user in post.rb
Post.first.destroy 

# Re generate slug for existing Post
Post.find_each(&:save)
```



{% embed url="http://norman.github.io/friendly\_id/file.Guide.html" %}









