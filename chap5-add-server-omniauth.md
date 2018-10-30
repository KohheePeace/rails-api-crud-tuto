# Chap5 Server Omniauth

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

{% embed url="https://github.com/laserlemon/figaro" %}

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



{% embed url="https://github.com/zquestz/omniauth-google-oauth2/issues/287" %}



{% embed url="https://stackoverflow.com/questions/38424133/getting-error-omniauthnosessionerror-with-rails-5-api" %}



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

### Rack-Cors <a id="rack-cors"></a>

{% embed url="https://github.com/cyu/rack-cors" %}

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

{% embed url="https://github.com/zquestz/omniauth-google-oauth2/issues/138" %}

{% embed url="https://github.com/zquestz/omniauth-google-oauth2/issues/308" %}

{% embed url="https://github.com/zquestz/omniauth-google-oauth2/blob/ea3035737e96c1d1e4dbd9058aa9aed73748caf3/examples/config.ru" %}

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

