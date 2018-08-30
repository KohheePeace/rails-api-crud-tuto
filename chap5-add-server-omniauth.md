# Chap5 Add Server Omniauth

{% code-tabs %}
{% code-tabs-item title="Gemfile" %}
```text
gem 'omniauth-google-oauth2'
```
{% endcode-tabs-item %}
{% endcode-tabs %}

```text
bundle
```

### Create config/initializers/omniauth.rb

{% code-tabs %}
{% code-tabs-item title="config/initializers/omniauth.rb" %}
```ruby
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :google_oauth2, ENV['GOOGLE_CLIENT_ID'], ENV['GOOGLE_CLIENT_SECRET']
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### Figaro

Use figaro gem to securely configure secrets key

{% embed data="{\"url\":\"https://github.com/laserlemon/figaro\",\"type\":\"link\",\"title\":\"laserlemon/figaro\",\"description\":\"figaro - Simple Rails app configuration\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars0.githubusercontent.com/u/34264?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}

{% code-tabs %}
{% code-tabs-item title="Gemfile" %}
```text
gem "figaro"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

```text
bundle
```

Run figaro command in terminal

```text
bundle exec figaro install
```

It create `application.yml` and edit `.gitignore`

```text
create  config/application.yml
append  .gitignore
```

In `application.yml` add Google client\_id and secret

{% code-tabs %}
{% code-tabs-item title="config/application.yml" %}
```yaml
development:
  GOOGLE_CLIENT_ID: "599452345899-rbpa86it53ba6pr6jtgsimbhpddkvdka.apps.googleusercontent.com"
  GOOGLE_CLIENT_SECRET: "ykEC_BodZTXlaSLSY0fFCDrY"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Add Routes for omniauth callback

```text
post '/auth/google_oauth2/callback', to: 'omniauth_callbacks#google_oauth2'
```

{% code-tabs %}
{% code-tabs-item title="config/routes.rb" %}
```ruby
Rails.application.routes.draw do
  root to: 'pages#index'

  namespace :api, path: '', constraints: { subdomain: 'api' } do
    post '/auth/google_oauth2/callback', to: 'omniauth_callbacks#google_oauth2'
    resources :posts
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Check routes

```text
rails routes
```

```text
api_auth_google_oauth2_callback POST   /auth/google_oauth2/callback(.:format)                                                   api/omniauth_callbacks#google_oauth2 {:subdomain=>"api"}
```

### Omniauth callback controller

```text
rails g controller api/omniauth_callbacks
```



### Add method for google\_oauth2 and check params

{% code-tabs %}
{% code-tabs-item title="controllers/api/omniauth\_callbacks.rb" %}
```ruby
class Api::OmniauthCallbacksController < ApplicationController
  def google_oauth2
    p "Check Auth !!!!!!!!!!!!!!!!"
    auth = request.env['omniauth.auth']
    p auth
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### Edit the client endpoint

Edit the url point of client

`url: 'http://api.localhost:3000/auth/google_oauth2/callback'`



{% code-tabs %}
{% code-tabs-item title="views/pages/index.html.erb" %}
```markup
<h1>Pages#Index</h1>
<button id="signinButton">Sign in with Google</button>

<script src="//ajax.googleapis.com/ajax/libs/jquery/1.8.2/jquery.min.js">
</script>
<script src="https://apis.google.com/js/client:platform.js?onload=start" async defer>
</script>
<script>
  function start() {
    gapi.load('auth2', function() {
      auth2 = gapi.auth2.init({
        client_id: '599452345899-rbpa86it53ba6pr6jtgsimbhpddkvdka.apps.googleusercontent.com',
        // Scopes to request in addition to 'profile' and 'email'
        //scope: 'additional_scope'
      });
    });
  }
</script>
<script>
  $('#signinButton').click(function() {
    // signInCallback defined in step 6.
    auth2.grantOfflineAccess().then(signInCallback);
  });
</script>
<script>
function signInCallback(authResult) {
  if (authResult['code']) {
    // Hide the sign-in button now that the user is authorized, for example:
    $('#signinButton').attr('style', 'display: none');
    // Send the code to the server
    /* We need to implement backend endpoint here */
    $.ajax({
      type: 'POST',
      url: 'http://api.localhost:3000/auth/google_oauth2/callback',
      // Always include an `X-Requested-With` header in every AJAX request,
      // to protect against CSRF attacks.
      headers: {
        'X-Requested-With': 'XMLHttpRequest'
      },
      contentType: 'application/octet-stream; charset=utf-8',
      success: function(result) {
        // Handle or verify the server response.
      },
      processData: false,
      data: authResult['code']
    });
  } else {
    // There was an error.
  }
}
</script>
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### Test !

```text
rails s
```



![](.gitbook/assets/sukurnshotto-2018-08-09-193911.png)



{% embed data="{\"url\":\"https://github.com/zquestz/omniauth-google-oauth2/issues/287\",\"type\":\"link\",\"title\":\"OmniAuth::NoSessionError \(You must provide a session to use OmniAuth.\) on Rails 5 --api · Issue \#287 · zquestz/omniauth-google-oauth2\",\"description\":\"Hello, thanks for this gem. I currently have the following problem: $ rails server =&gt; Booting WEBrick ...blah blah Started GET &quot;/&quot; for 127.0.0.1 at 2017-06-24 18:09:45 +0200 ...blah bl...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars1.githubusercontent.com/u/3307327?s=400&v=4\",\"width\":420,\"height\":420,\"aspectRatio\":1}}" %}



{% embed data="{\"url\":\"https://stackoverflow.com/questions/38424133/getting-error-omniauthnosessionerror-with-rails-5-api\",\"type\":\"link\",\"title\":\"Getting error OmniAuth::NoSessionError with Rails 5 API\",\"description\":\"I created a new Rails 5 application with rails new appname --api which seems great! I want to use it as a backend to a frontend with React and in time a Chrome App. For now I want to create an API....\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.sstatic.net/Sites/stackoverflow/img/apple-touch-icon.png?v=c78bd457575a\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://cdn.sstatic.net/Sites/stackoverflow/img/apple-touch-icon@2.png?v=73d79a89bded\",\"width\":316,\"height\":316,\"aspectRatio\":1}}" %}



{% code-tabs %}
{% code-tabs-item title="config/application.rb" %}
```ruby
config.middleware.use ActionDispatch::Cookies
config.middleware.use ActionDispatch::Session::CookieStore
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Restart server 

```text
rails s
```



### Test again! 

### It shows CORS error

![](.gitbook/assets/sukurnshotto-2018-08-10-81718.png)



![](.gitbook/assets/sukurnshotto-2018-08-09-204953.png)

### Rack-Cors {#rack-cors}

{% embed data="{\"url\":\"https://github.com/cyu/rack-cors\",\"type\":\"link\",\"title\":\"cyu/rack-cors\",\"description\":\"rack-cors - Rack Middleware for handling Cross-Origin Resource Sharing \(CORS\), which makes cross-origin AJAX possible.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars2.githubusercontent.com/u/2431?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}

{% code-tabs %}
{% code-tabs-item title="Gemfile" %}
```text
gem 'rack-cors'
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="config/initializers/cors.rb" %}
```ruby
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins '*'
    resource '*',
      headers: :any,
      methods: [:get, :post, :put, :patch, :delete, :options, :head]
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### Test again!

```text
rails s
```



### It shows internal error

![](.gitbook/assets/sukurnshotto-2018-08-10-165708.png)

In terminal logs, 

### it shows csrf error

![Teminal Error log](.gitbook/assets/sukurnshotto-2018-08-09-201102.png)

{% embed data="{\"url\":\"https://github.com/zquestz/omniauth-google-oauth2/issues/138\",\"type\":\"link\",\"title\":\"CSRF error · Issue \#138 · zquestz/omniauth-google-oauth2\",\"description\":\"Hello, I am using this gem to handle google oauth authentication and I encountered a strange behaviour. When I authenticate with google, everything works fine : redirected to google, consent screen...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars3.githubusercontent.com/u/271047?s=400&v=4\",\"width\":200,\"height\":200,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"https://github.com/zquestz/omniauth-google-oauth2/issues/308\",\"type\":\"link\",\"title\":\"CSRF issue · Issue \#308 · zquestz/omniauth-google-oauth2\",\"description\":\"Can&\#39;t say that this is a bug but I am stumped on how to solve it. Maybe posting it hear will bring an idea to someone&\#39;s mind. I&\#39;ve written a super simple Rails program to verify the pro...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars0.githubusercontent.com/u/44681?s=400&v=4\",\"width\":336,\"height\":336,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"https://github.com/zquestz/omniauth-google-oauth2/blob/ea3035737e96c1d1e4dbd9058aa9aed73748caf3/examples/config.ru\",\"type\":\"link\",\"title\":\"zquestz/omniauth-google-oauth2\",\"description\":\"omniauth-google-oauth2 - Oauth2 strategy for Google\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars2.githubusercontent.com/u/83898?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}

{% code-tabs %}
{% code-tabs-item title="config/initializers/omniauth.rb" %}
```ruby
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :google_oauth2, ENV['GOOGLE_CLIENT_ID'], ENV['GOOGLE_CLIENT_SECRET'], provider_ignores_state: true
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### Test again!

```text
rails s
```

### It shows internal erros

![](.gitbook/assets/sukurnshotto-2018-08-10-170238.png)

So, In terminal logs, there is 

### Missing required parameter: code

![](.gitbook/assets/sukurnshotto-2018-08-10-170339.png)

### Need to edit client ajax call

Need to remove unnecessary params of ajax call.

{% code-tabs %}
{% code-tabs-item title="views/pages/index.html.erb" %}
```markup
<h1>Pages#Index</h1>
<button id="signinButton">Sign in with Google</button>

<script src="//ajax.googleapis.com/ajax/libs/jquery/1.8.2/jquery.min.js">
</script>
<script src="https://apis.google.com/js/client:platform.js?onload=start" async defer>
</script>
<script>
  function start() {
    gapi.load('auth2', function() {
      auth2 = gapi.auth2.init({
        client_id: '599452345899-rbpa86it53ba6pr6jtgsimbhpddkvdka.apps.googleusercontent.com',
        // Scopes to request in addition to 'profile' and 'email'
        //scope: 'additional_scope'
      });
    });
  }
</script>
<script>
  $('#signinButton').click(function() {
    // signInCallback defined in step 6.
    auth2.grantOfflineAccess().then(signInCallback);
  });
</script>
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



### Test again!

![](.gitbook/assets/sukurnshotto-2018-08-10-170721.png)

### Need to Enable google+ api

[https://console.developers.google.com/apis/api/plus.googleapis.com/overview?project=rails-api-crud-tuto-demo&duration=PT1H](https://console.developers.google.com/apis/api/plus.googleapis.com/overview?project=rails-api-crud-tuto-demo&duration=PT1H)

![](.gitbook/assets/sukurnshotto-2018-08-10-170840.png)



### Test again !

![](.gitbook/assets/sukurnshotto-2018-08-10-171429.png)



In the next chapter, we will create user by using the information from google oauth.

