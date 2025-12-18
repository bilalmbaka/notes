### Apache 2

## Macos

```bash
 > sudo homebrew install apache2
```

The configuration file is found in *__/opt/homebrew/bin/apache2__*

```bash
> sudo apachectl start
```

*Fix lynx: command not found*
```bash
> sudo homebrew install lynx
```
or <br/>

edit /opt/homebrew/bin/apache2

edit lynx="echo"

*Files are stored in*
__/opt/homebrew/var/www__

# Macos
## Clawing back space.

1. flutter clean
2. ./gradlew clean
3. rm ~/.gradle/caches/transforms
4. rm -rf ~/Library/Developer/Xcode/Archives
Or open Xcode → Window → Organizer → Archives and delete selectively.
5. pod cache clean --all
6. per project
   rm -rf Pods
   rm -rf Podfile.lock
   pod install
7. rm -rf ~/Library/Developer/Xcode/DerivedData
8. delete unused images
    ~/Library/Android/sdk/system-images
9. delete old build tools
    ~/Library/Android/sdk/build-tools
10. delete old platforms
    ~/Library/Android/sdk/platforms
11. delete old ndks
    ~/Library/Android/sdk/ndk
12. ~/Libraray/Caches/Google/AndroidStudio~


# MiSC

### show hidden folders.
Cmd + Shift + .