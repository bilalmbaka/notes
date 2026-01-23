# Setup

## Set webhook

```curl
https://api.telegram.org/<api>/setWebhook
```

Use this method to specify a URL and receive incoming updates via an outgoing webhook.
Whenever there is an update for the bot, we will send an HTTPS POST request to the
specified URL, containing a JSON-serialized Update. In case of an unsuccessful
request (a request with response HTTP status code different from 2XY), we will
repeat the request and give up after a reasonable amount of attempts. Returns True on success.

Parameters::
url,certificate,ip_address,max_connections <br/>

allowed_updates = A JSON-serialized list of the update types you want your bot to receive.
For example, specify ["message", "edited_channel_post", "callback_query"] to only receive
updates of these types. See Update for a complete list of available update types.
Specify an empty list to receive all update types except chat_member, message_reaction,
and message_reaction_count (default). If not specified, the previous setting will be used.
Please note that this parameter doesn't affect updates created before the call to the
setWebhook, so unwanted updates may be received for a short period of time.

drop_pending_updates = Pass True to drop all pending updates

secret*token = A secret token to be sent in a header “X-Telegram-Bot-Api-Secret-Token”
in every webhook request, 1-256 characters. Only characters A-Z, a-z, 0-9, * and - are
allowed. The header is useful to ensure that the request comes from a webhook set by you.

<br/>
<br/>

_**NOTE**: You will not be able to receive updates using getUpdates for as long as an outgoing webhook is set up._

<br/><br/>

## deleteWebhook

```curl
https://api.telegram.org/<api>/setWebhook
```

Use this method to remove webhook integration if you decide to switch back to getUpdates. Returns True on success.

Parameters:
drop_pending_updates (optional) = Pass True to drop all pending updates

### getWebhookInfo

```curl
https://api.telegram.org/<api>/getWebhookInfo
```

Use this method to get current webhook status. Requires no parameters. On success, returns a WebhookInfo object.
If the bot is using getUpdates, will return an object with the url field empty.
