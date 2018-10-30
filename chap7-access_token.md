# Chap7 Access\_Token

### How to implement secure token

Please look at the below url ! 

{% embed url="https://stackoverflow.com/questions/18605294/is-devises-token-authenticatable-secure" %}

It shows ...

> To have the most secure token authentication mechanism, the token:
>
> 1. Must be sent via HTTPS.
> 2. Must be random, of cryptographic strength.
> 3. Must be securely compared.
> 4. Must not be stored directly in the database. Only a hash of the token can be stored there. \(Remember, token = password. We don't store passwords in plain text in the db, right?\)
> 5. Should expire according to some logic.



And below discussions

{% embed url="https://github.com/gonzalo-bulnes/simple\_token\_authentication/issues/82" %}



**About secure compare**

{% embed url="https://github.com/adamniedzielski/tiddle/issues/35" %}



**Logins for multiple client**

[Q: Support for multiple logins by the same user with different mobile devices? \(R: Not yet, but here are some pointers!\)](https://github.com/gonzalo-bulnes/simple_token_authentication/issues/135#issuecomment-103938998)



### Plan Conclusion

1. Follow the stackoverflow answer for secure token
2. Support logins for multiple client \(Like [Tiddle](https://github.com/adamniedzielski/tiddle) gem\)



