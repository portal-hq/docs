# @portal-hq/icloud-storage

## Portal iCloud backup storage

The `@portal-hq/icloud-storage` package contains a storage adapter intended for use with the `@portal-hq/core` package.

This storage adapter allows end users to save backup MPC shares to their iCloud filesystem.

_**Note**: This storage provider is only supported on iOS devices, so should not be used as your sole backup storage adapter unless you are explicitly releasing an iOS-only app._

_**Note:** you will need a paid developer account to use iCloud backup_&#x20;

### Basic usage

#### Install the package in your React Native project.

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add @portal-hq/icloud-storage
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save @portal-hq/icloud-storage
```
{% endtab %}
{% endtabs %}

#### Import the package into the component where you initialize the Portal class

```typescript
import Portal from '@portal-hq/core'
import ICloudStorage from '@portal-hq/icloud-storage'
```

#### Register your storage adapter and initialize the Portal class

```typescript
const portal = new Portal({
  // ...your other config options
  backup: {
    icloud: new ICloudStorage(),
  },
})
```

#### Check if the user is connected to iCloud

```typescript
// Returns one of the following:
// "available", "not_signed_in", "no_access"
await portal.backup[BackupMethods.iCloud].getAvailability();
```

### Setting up iCloud Backups in your iOS app

1. Open your project in XCode.
2.  Navigate to `Signing & Capabilities`:&#x20;

    <figure><img src="https://user-images.githubusercontent.com/165256/187792977-8e2b4388-f276-40ea-a835-3cf05183b8d9.png" alt=""><figcaption></figcaption></figure>
3.  Click the `+` Button right below it, scroll down and select `iCloud`.

    <figure><img src="https://user-images.githubusercontent.com/165256/187793954-e94496b8-0b7f-47c7-9117-92c31bf79c67.png" alt=""><figcaption></figcaption></figure>
4. Under iCloud Services select `Key-value storage`.

<figure><img src="../../../../.gitbook/assets/Screen Shot 2022-11-09 at 8.05.20 PM.png" alt=""><figcaption></figcaption></figure>
