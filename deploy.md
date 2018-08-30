# Chap16 Heroku Deploy

```text
heroku create
```



```text
git push heroku master
```



```text
heroku run rake db:migrate
```



### Add google api key and secrets for production

Visit...

[https://console.developers.google.com/apis/credentials?project=rails-api-crud-tuto-demo](https://console.developers.google.com/apis/credentials?project=rails-api-crud-tuto-demo)

![](.gitbook/assets/sukurnshotto-2018-08-13-132043.png)



{% code-tabs %}
{% code-tabs-item title="application.yml" %}
```text
development:
  GOOGLE_CLIENT_ID: "599452344425899-.apps..com"
  GOOGLE_CLIENT_SECRET: "fjioejfwe"

production:
  GOOGLE_CLIENT_ID: "745665442669-ir6a0pqtatp980m4amifveaqjug119s4.apps.googleusercontent.com"
  GOOGLE_CLIENT_SECRET: "j43ihfih34543439"

```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Set Production Google oauth id and keys

```text
figaro heroku:set -e production
```



[https://devcenter.heroku.com/articles/getting-started-with-rails5](https://devcenter.heroku.com/articles/getting-started-with-rails5)





{% embed data="{\"url\":\"https://blog.heroku.com/a-rock-solid-modern-web-stack\",\"type\":\"link\",\"title\":\"A Rock Solid, Modern Web Stack—Rails 5 API + ActiveAdmin + Create React App on Heroku\",\"description\":\"Blending a rock-solid CMS and API with the absolute best in front-end tooling, built as a single project and hosted seamlessly on Heroku.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://www.herokucdn.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://www.herokucdn.com/images/og.png\",\"width\":1200,\"height\":630,\"aspectRatio\":0.525}}" %}





### Godaddy domain settings

{% embed data="{\"url\":\"https://github.com/IcaliaLabs/guides/wiki/Domain-configuration-using-Heroku-&-GoDaddy\",\"type\":\"link\",\"title\":\"IcaliaLabs/guides\",\"description\":\"guides - A set of rules we use at @icalialabs to build better software\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars0.githubusercontent.com/u/2523244?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}



