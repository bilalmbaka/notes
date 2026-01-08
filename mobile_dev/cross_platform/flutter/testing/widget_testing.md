### sources

[Flutter doc widget test](https://docs.flutter.dev/cookbook/testing/widget/introduction)

After the initial call to pumpWidget(), the WidgetTester provides additional ways to rebuild the same widget. This is useful if you're working with a StatefulWidget or animations.

- tester.pump(Duration duration)
- tester.pumpAndSettle()

<br/>
<br/>

# Testing with riverpod

First you mock repo classes and service classes, then override the different riverpod classes.

```dart
class TestHelpers {
  static Widget buildRoute({
    required Route<dynamic>? Function(RouteSettings) routes,
    List<Override> overrides = const [],
  }) {
    return ProviderScope(
      overrides: overrides,
        child: MaterialApp(
          restorationScopeId: AppConstants.rootRestorationId,
          title: AppStrings.appName,
          navigatorKey: navigatorkey,
        ),
    );
  }
}
```

```dart
await tester.pumpWidget(
    TestHelpers.buildRoute(
        routes: LoginRoute.generateRoute,
        overrides: [
          loginViewModel.overrideWith(() {
            return LoginViewModel(
              authUseCase: AuthUsecase(
                profileRepository: fakeProfileRepository,
                authRepository: mockAuthRepository,
              ),
            );
          }),
          saveLoginViewModel.overrideWith(() {
            return SavedLoginViewModel(
              authUseCase: AuthUsecase(
                profileRepository: fakeProfileRepository,
                authRepository: mockAuthRepository,
              ),
            );
          }),
        ],
      ),
);
```


<br>
<br>
<br>

# Scolling in widget tests

```dart
//Scroll down.
await tester.dragUntilVisible(
    find.text(AppStrings.dontHaveAnAccount),
    find.byKey(
        AppConstants.loginScreenKey,
    ),
    const Offset(0, 10),
);

await tester.pumpAndSettle();
```

