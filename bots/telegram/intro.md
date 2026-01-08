# Sources

[Telegram documentation](https://core.telegram.org/bots/api#getting-updates)

# Setup

Message @botfather, then get the apitoken.

# Making requests

All queries to the Telegram Bot API must be served over HTTPS and need to be presented in this form:

```
https://api.telegram.org/bot<token>/METHOD_NAME. Like this for example:
https://api.telegram.org/bot123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11/getMe
```

The response contains a JSON object, which always has a Boolean field 'ok' and may have an optional
String field 'description' with a human-readable description of the result. If 'ok' equals True,
the request was successful and the result of the query can be found in the 'result' field. In case
of an unsuccessful request, 'ok' equals false and the error is explained in the 'description'.
An Integer 'error_code' field is also returned, but its contents are subject to change in the
future. Some errors may also have an optional field 'parameters' of the type ResponseParameters,
which can help to automatically handle the error.

All methods in the Bot API are case-insensitive.
All queries must be made using UTF-8.

<br/>
<br/>

# Getting updates

There are two mutually exclusive ways of receiving updates for your bot - the getUpdates method
on one hand and webhooks on the other. Incoming updates are stored on the server until the bot
receives them either way, but they will not be kept longer than 24 hours.

Regardless of which option you choose, you will receive JSON-serialized Update objects as a result.
