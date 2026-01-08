### Go routers

```dart
 return MaterialApp.router(
          restorationScopeId: AppConstants.appRestorationScopeId,
          title: AppStrings.appName,
          debugShowCheckedModeBanner: false,
          routerConfig: goRouterProvider.value,
          theme: (themeProvider) ? darkTheme : lightTheme,
 )
```
