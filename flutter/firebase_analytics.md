```bash
> flutter pub add firebase_analytics
```

```dart
FirebaseAnalytics analytics = FirebaseAnalytics.instance;
```

### Logging events
[Documentation] (https://firebase.google.com/docs/analytics/events?platform=flutter)

```dart
await FirebaseAnalytics.instance
  .logBeginCheckout(
    value: 10.0,
    currency: 'USD',
    items: [
      AnalyticsEventItem(
        itemName: 'Socks',
        itemId: 'xjw73ndnw',
        price: '10.0'
      ),
    ],
    coupon: '10PERCENTOFF'
  );
```

```dart
await FirebaseAnalytics.instance.logEvent(
    name: "select_content",
    parameters: {
        "content_type": "image",
        "item_id": itemId,
    },
);
```

_Set default event parameters_

You can log parameters across events using setDefaultEventParameters(). 
Default parameters are associated with all future events that are logged.

As with custom parameters, register the default event parameters to ensure 
they appear in Analytics reports.

Valid parameter values are String and num. Setting a key's value to null 
clears that parameter. Passing in a null value clears all parameters.

```dart
// Not supported on web
await FirebaseAnalytics.instance
  .setDefaultEventParameters({
    version: '1.2.3'
  });
```

If a parameter is specified in the logEvent() or log- method, that value 
is used instead of the default.

To clear a default parameter, call the setDefaultEventParameters() method 
with the parameter set to null.

#### [GA4] Recommended events

[Recomended events](https://support.google.com/analytics/answer/9267735)
