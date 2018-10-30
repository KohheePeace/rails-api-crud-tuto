# Chap16 Heroku Deploy

```text
heroku create appname
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





{% embed url="https://blog.heroku.com/a-rock-solid-modern-web-stack" %}





### Godaddy domain settings

{% embed url="https://github.com/IcaliaLabs/guides/wiki/Domain-configuration-using-Heroku-&-GoDaddy" %}



