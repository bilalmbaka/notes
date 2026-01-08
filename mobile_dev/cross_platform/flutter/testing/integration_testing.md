# Integration testing

It is pretty much the same as widget testing you just add.

```dart
  IntegrationTestWidgetsFlutterBinding.ensureInitialized();
````

to the start of main function.

To run the integration test

```bash
> flutter test integration_test/screen_name.test.dart --flavor dev
```


