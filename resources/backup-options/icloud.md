# iCloud

This storage adapter allows end users to save backup MPC shares to their iCloud filesystem.

_**Note**: This storage provider is only supported on iOS devices, so should not be used as your sole backup storage adapter unless you are explicitly releasing an iOS-only app._

_**Note:** you will need a paid developer account to use iCloud backup_&#x20;

_**Note:**_ iCloud recovery is tied to the application's bundle identifier. This means that data can only be restored within the same app identified by that bundle ID. If you're migrating your app to a different technology (e.g., from iOS Native to React Native), the new version of the app must be released using the same bundle identifier as the original. Changing the bundle identifier will prevent iCloud backups from being restored in the new app version.

### Setting up iCloud Backups in your iOS app

1. Open your project in XCode.
2.  Navigate to `Signing & Capabilities`:&#x20;

    <div data-full-width="true"><figure><img src="https://user-images.githubusercontent.com/165256/187792977-8e2b4388-f276-40ea-a835-3cf05183b8d9.png" alt=""><figcaption></figcaption></figure></div>
3.  Click the `+` Button right below it, scroll down and select `iCloud`.

    <figure><img src="https://user-images.githubusercontent.com/165256/187793954-e94496b8-0b7f-47c7-9117-92c31bf79c67.png" alt=""><figcaption></figcaption></figure>
4. Under iCloud Services select `Key-value storage`.

<figure><img src="../../.gitbook/assets/Screen Shot 2022-11-09 at 8.05.20 PM.png" alt=""><figcaption></figcaption></figure>
