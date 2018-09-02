# Pre Setup

{% code-tabs %}
{% code-tabs-item title="11" %}
```text
rails new rails-api-crud-tuto --api -T --database=postgresql
cd rails-api-crud-tuto
rails db:create
```
{% endcode-tabs-item %}
{% endcode-tabs %}

`-T` option is ...

```text
  rails -h
  ...
  -T, [--skip-test], [--no-skip-test] # Skip test files
```

### Gem for auto check

1. [brakeman](https://github.com/presidentbeef/brakeman) - for security check
2. [annotate\_models](https://github.com/ctran/annotate_models) - Annotate Rails classes with schema and routes info
3. [rails\_best\_practices](https://github.com/flyerhzm/rails_best_practices) - a code metric tool for rails projects [http://rails-bestpractices.com](http://rails-bestpractices.com)
4. [bullet](https://github.com/flyerhzm/bullet) - help to kill N + 1 problem
5. [lol\_dba](https://github.com/plentz/lol_dba) - scan your application models and displays a list of columns that probably should be indexed \(not currently add\)

**1** [**brakeman**](https://github.com/presidentbeef/brakeman)\*\*\*\*

{% code-tabs %}
{% code-tabs-item title="Gemfile" %}
```text
group :development do
  gem 'brakeman'
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

```text
brakeman
```

**2** [**annotate\_models**](https://github.com/ctran/annotate_models)\*\*\*\*

To _**automatically**_ annotate every time you run **db:migrate**, either run 

{% code-tabs %}
{% code-tabs-item title="Gemfile" %}
```text
group :development do
  gem 'annotate'
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

```text
rails g annotate:install
```

```text
Running via Spring preloader in process 53096
      create  lib/tasks/auto_annotate_models.rake
```

[https://github.com/ctran/annotate\_models/blob/develop/README.rdoc\#configuration-in-rails](https://github.com/ctran/annotate_models/blob/develop/README.rdoc#configuration-in-rails)

**3** [**rails\_best\_practices**](https://github.com/flyerhzm/rails_best_practices)\*\*\*\*

{% code-tabs %}
{% code-tabs-item title="Gemfile" %}
```text
group :development do
  gem 'rails_best_practices'
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

At the root directory of a Rails app, run:

```text
rails_best_practices .
```

Or for HTML output:

```text
rails_best_practices -f html .
```

By default rails\_best\_practices will parse code in the `vendor`, `spec`, `test` and `features` directories.

**4** [**bullet**](https://github.com/flyerhzm/bullet)\*\*\*\*

```text
group :development do
  gem 'bullet'
end
```

[https://github.com/flyerhzm/bullet\#demo](https://github.com/flyerhzm/bullet#demo)

Enable the Bullet gem with generate command

```text
bundle exec rails g bullet:install
```

```text
Running via Spring preloader in process 49515
Enabled bullet in config/environments/development.rb
Would you like to enable bullet in test environment? (y/n) 
n
```

```text
git status
    
    modified:   config/environments/development.rb
```

```text
git diff 

-  # Settings specified here will take precedence over those in config/application.rb.
+  config.after_initialize do
+      Bullet.enable        = true
+      Bullet.alert         = true
+      Bullet.bullet_logger = true
+      Bullet.console       = true
+    # Bullet.growl         = true
+      Bullet.rails_logger  = true
+      Bullet.add_footer    = true
+    end  # Settings specified here will take precedence over those in config/application.rb.
```







### Gem for rspec testing 

1. [rspec-rails](https://github.com/rspec/rspec-rails) - Testing framework.
2. [factory\_bot\_rails](https://github.com/thoughtbot/factory_bot_rails) - A fixtures replacement with a more straightforward syntax. You'll see.
3. [shoulda\_matchers](https://github.com/thoughtbot/shoulda-matchers) - Provides RSpec with additional matchers.
4. [database\_cleaner](https://github.com/DatabaseCleaner/database_cleaner) - You guessed it! It literally cleans our test database to ensure a clean state in each test suite.
5. [rspec-guard](https://github.com/guard/guard-rspec) - auto run rspec

**Configuration for test**

#### **1** [**rspec-rails**](https://github.com/rspec/rspec-rails)\*\*\*\*

```text
group :development, :test do
  gem 'rspec-rails', '~> 3.7'
end
```

```text
rails generate rspec:install
```

```text
create  .rspec
create  spec
create  spec/spec_helper.rb
create  spec/rails_helper.rb
```

This adds the following files which are used for configuration:

* `.rspec`
* `spec/spec_helper.rb`
* `spec/rails_helper.rb`

#### 2 [factory\_bot\_rails](https://github.com/thoughtbot/factory_bot_rails)

See

[https://www.rubydoc.info/gems/factory\_bot/file/GETTING\_STARTED.md](https://www.rubydoc.info/gems/factory_bot/file/GETTING_STARTED.md)

{% code-tabs %}
{% code-tabs-item title="Gemfile" %}
```text
group :development, :test do
  gem 'factory_bot_rails'
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

In `spec/rails_helper.rb`

{% code-tabs %}
{% code-tabs-item title="spec/rails\_helper.rb" %}
```ruby
...
RSpec.configuration do |config|
  ...
  # add `FactoryBot` methods
  config.include FactoryBot::Syntax::Methods
  ...
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

**\*factory\_bot generate command**  
[https://github.com/thoughtbot/factory\_bot\_rails/issues/263](https://github.com/thoughtbot/factory_bot_rails/issues/263)  
[thoughtbot/factory\_bot\_rails\#263](https://github.com/thoughtbot/factory_bot_rails/issues/263)  
[https://stackoverflow.com/questions/11702265/can-factorygirl-generate-factories-after-your-models-have-been-created](https://stackoverflow.com/questions/11702265/can-factorygirl-generate-factories-after-your-models-have-been-created)

{% code-tabs %}
{% code-tabs-item title="application.rb" %}
```ruby
class Application < Rails::Application
    ...
    
    # generate factory by manually
    # config.generators do |g|
    #   g.factory_bot dir: 'spec/factories'
    # end
    
    # auto generate facory when run 'rails g model' command
    config.generators do |g| 
      # don't generate controller spec when generate controller for api
      g.controller_specs false
      g.fixture_replacement :factory_bot, dir: 'spec/factories' 
    end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

**Example:** auto generate factory

```text
rails g model Book title author:references
```

```ruby
$ rails g model book title author:references
Running via Spring preloader in process 32137
      invoke  active_record
      create    db/migrate/20180616023518_create_books.rb
      create    app/models/book.rb
      invoke    rspec
      create      spec/models/book_spec.rb
      invoke      factory_bot
      create        spec/factories/books.rb
```

**Example:** create factory manually

```text
rails g factory_bot:model Car name:string speed:integer
```

**Example:** delete factory manally

```text
rails d factory_bot:model Car
```

#### 3 [shoulda\_matchers](https://github.com/thoughtbot/shoulda-matchers)

{% code-tabs %}
{% code-tabs-item title="Gemfile" %}
```text
group :test do
  gem 'shoulda-matchers', '~> 3.1'
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

In `spec/rails_helper.rb`

{% code-tabs %}
{% code-tabs-item title="spec/rails\_helper.rb" %}
```ruby
...
# configure shoulda matchers to use rspec as the test framework and full matcher libraries for rails
Shoulda::Matchers.configure do |config|
  config.integrate do |with|
    with.test_framework :rspec
    with.library :rails
  end
end

...
RSpec.configuration do |config|
  ...
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

#### 4 [database\_cleaner](https://github.com/DatabaseCleaner/database_cleaner)

{% code-tabs %}
{% code-tabs-item title="Gemfile" %}
```text
group :test do
  gem 'database_cleaner'
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

In `spec/rails_helper.rb`

{% code-tabs %}
{% code-tabs-item title="spec/rails\_helper.rb" %}
```ruby
# require database cleaner at the top level
require 'database_cleaner'

...
RSpec.configuration do |config|
  ...
  # start by truncating all the tables but then use the faster transaction strategy the rest of the time.
  config.before(:suite) do
    DatabaseCleaner.clean_with(:truncation)
    DatabaseCleaner.strategy = :transaction
  end

  # start the transaction strategy as examples are run
  config.around(:each) do |example|
    DatabaseCleaner.cleaning do
      example.run
    end
  end
  ...
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

\*\*\*\*

#### **5** [**Guard-Rspec**](https://github.com/guard/guard-rspec)

```text
group :development do
  gem 'guard-rspec', require: false
end
```

{% code-tabs %}
{% code-tabs-item title="terminal" %}
```text
bundle exec guard init rspec
```
{% endcode-tabs-item %}
{% endcode-tabs %}



#### .rspec

```text
--require spec_helper
--format documentation
--color
```

### Final Gemfile

{% code-tabs %}
{% code-tabs-item title="Gemfile" %}
```ruby
source 'https://rubygems.org'
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

ruby '2.4.1'

# Bundle edge Rails instead: gem 'rails', github: 'rails/rails'
gem 'rails', '~> 5.2.0'
# Use postgresql as the database for Active Record
gem 'pg', '>= 0.18', '< 2.0'
# Use Puma as the app server
gem 'puma', '~> 3.11'
# Build JSON APIs with ease. Read more: https://github.com/rails/jbuilder
# gem 'jbuilder', '~> 2.5'
# Use Redis adapter to run Action Cable in production
# gem 'redis', '~> 4.0'
# Use ActiveModel has_secure_password
# gem 'bcrypt', '~> 3.1.7'

# Use ActiveStorage variant
# gem 'mini_magick', '~> 4.8'

# Use Capistrano for deployment
# gem 'capistrano-rails', group: :development

# Reduces boot times through caching; required in config/boot.rb
gem 'bootsnap', '>= 1.1.0', require: false

# Use Rack CORS for handling Cross-Origin Resource Sharing (CORS), making cross-origin AJAX possible
# gem 'rack-cors'

group :development, :test do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
  gem 'rspec-rails', '~> 3.7'
  gem 'factory_bot_rails'
end

group :development do
  gem 'listen', '>= 3.0.5', '< 3.2'
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
  gem 'brakeman'
  gem 'annotate'
  gem 'rails_best_practices'
  gem 'bullet'
  gem 'guard-rspec', require: false
end

group :test do
  gem 'shoulda-matchers', '~> 3.1'
  gem 'database_cleaner'
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]

```
{% endcode-tabs-item %}
{% endcode-tabs %}



### Clean Gemfile

```ruby
source 'https://rubygems.org'
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

ruby '2.5.1'

gem 'rails', '~> 5.2.1'
gem 'pg', '>= 0.18', '< 2.0'
gem 'puma', '~> 3.11'
gem 'bcrypt', '~> 3.1.7'
gem 'bootsnap', '>= 1.1.0', require: false
gem 'rack-cors'

group :development, :test do
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
  gem 'rspec-rails', '~> 3.7'
  gem 'factory_bot_rails'
end

group :development do
  gem 'listen', '>= 3.0.5', '< 3.2'
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
  gem 'brakeman'
  gem 'annotate'
  gem 'rails_best_practices'
  gem 'bullet'
  gem 'guard-rspec', require: false
end

group :test do
  gem 'shoulda-matchers', '~> 3.1'
  gem 'database_cleaner'
end

gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]

```



### Finish!

