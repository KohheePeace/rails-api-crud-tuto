# Chap2 Authentication Overview

### Session or Token Authentication ?

There are many many tutorial, stackoverflow and blog post etc ...

I was exhausted from searching about that :\(

But the below one is really really really well summarized.

{% embed url="https://hashedin.com/blog/auth-headers-vs-jwt-vs-sessions-choosing-right-auth-technique-for-apis/" %}

{% embed url="https://docs.google.com/spreadsheets/d/1tAX5ZJzluilhoYKjra-uHbMCZraaQkqIHl3RIQ8mVkM/edit\#gid=0" %}

> ### Use Case 1. API for Single Page Application
>
> 1. **Will mobile applications to use the same APIs?** If yes, prefer token-based authentication \(but ensure a separate API is built for these use cases\)

### What about Jwt ?

{% embed url="http://cryto.net/~joepie91/blog/2016/06/13/stop-using-jwt-for-sessions/" %}

{% embed url="https://github.com/shieldfy/API-Security-Checklist/issues/6" %}

{% embed url="https://scotch.io/bar-talk/why-jwts-suck-as-session-tokens" %}

{% embed url="https://speakerdeck.com/rdegges/jwts-suck-and-are-stupid" %}

### Conclusion about authentication strategy

 **=&gt; Use token authentication without jwt**

Below resources are the token authentication flow without jwt.

{% embed url="http://railscasts.com/episodes/352-securing-an-api?view=asciicast" %}

{% embed url="https://github.com/lynndylanhurley/devise\_token\_auth" %}

{% embed url="https://github.com/gonzalo-bulnes/simple\_token\_authentication" %}

{% embed url="https://github.com/adamniedzielski/tiddle" %}





### Login Strategy

![](.gitbook/assets/screen-shot-2018-08-08-at-9.53.19.png)

This is a Airbnb Sign\_up page.

In Airbnb, there are 3 options to sign up.

1. Facebook Login
2. Google Login
3. Email Login

### Social Login

Facebook Login and Google Login are both "Social Login".

We don't need to store "password".

### Email Login

We need to ask user to create "password" and then store it in our database.

_Are you use same password across multiple sites ?_

My answer is _Yes_.

This means we needs to really really care about security.

![](.gitbook/assets/screen-shot-2018-08-08-at-16.52.56.png)

### Conclusion about login strategy

I choose to use 

_**Google Login**_

**=&gt; Simple, Enough, and guarantee that user have email**

Facebook user who sign up by phone number don't have email.

![Facebook sign up page](.gitbook/assets/screen-shot-2018-08-08-at-17.01.04.png)



