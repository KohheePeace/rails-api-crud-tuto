# Chap7 Access\_Token

### How to implement secure token

Please look at the below url ! 

{% embed data="{\"url\":\"https://stackoverflow.com/questions/18605294/is-devises-token-authenticatable-secure\",\"type\":\"link\",\"title\":\"Is devise\'s token\_authenticatable secure?\",\"description\":\"I\'m building a simple api with Rails API, and want to make sure I\'m on the right track here. I\'m using devise to handle logins, and decided to go with Devise\'s token\_authenticatable option, which\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.sstatic.net/Sites/stackoverflow/img/apple-touch-icon.png?v=c78bd457575a\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://cdn.sstatic.net/Sites/stackoverflow/img/apple-touch-icon@2.png?v=73d79a89bded\",\"width\":316,\"height\":316,\"aspectRatio\":1}}" %}

It shows ...

> To have the most secure token authentication mechanism, the token:
>
> 1. Must be sent via HTTPS.
> 2. Must be random, of cryptographic strength.
> 3. Must be securely compared.
> 4. Must not be stored directly in the database. Only a hash of the token can be stored there. \(Remember, token = password. We don't store passwords in plain text in the db, right?\)
> 5. Should expire according to some logic.



And below discussions

{% embed data="{\"url\":\"https://github.com/gonzalo-bulnes/simple\_token\_authentication/issues/82\",\"type\":\"link\",\"title\":\"Is it safe to store authentication tokens in plain text? · Issue \#82 · gonzalo-bulnes/simple\_token\_authentication\",\"description\":\"It seems like this gem stores the authentication tokens in plain text. Doesn&\#39;t this pose a security risk? If anyone ever got read access to the database, they could have everyone&\#39;s token. W...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars0.githubusercontent.com/u/244766?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}



**About secure compare**

{% embed data="{\"url\":\"https://github.com/adamniedzielski/tiddle/issues/35\",\"type\":\"link\",\"title\":\"Remove unnecessary secure compare · Issue \#35 · adamniedzielski/tiddle\",\"description\":\"Thanks @DamirSvrtan for explaining this to me! 💚 Here we load all the tokens for the given user into the memory and perform secure compare on them. Secure compare was necessary to prevent timing at...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars1.githubusercontent.com/u/2426386?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}



**Logins for multiple client**

[Q: Support for multiple logins by the same user with different mobile devices? \(R: Not yet, but here are some pointers!\)](https://github.com/gonzalo-bulnes/simple_token_authentication/issues/135#issuecomment-103938998)



### Plan Conclusion

1. Follow the stackoverflow answer for secure token
2. Support logins for multiple client \(Like [Tiddle](https://github.com/adamniedzielski/tiddle) gem\)



