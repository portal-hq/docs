# @portal-hq/keychain

The @portal-hq/keychain package contains a storage adapter intended for use with the @portal-hq/core package. This storage adapter allows end users to save MPC shares to their local device keychain us

### Basic usage

{% hint style="info" %}
Adding **Face ID** support on iOS:

Add the following tags to your `info.plist` file if it does not already exist:

```xml
<key>NSFaceIDUsageDescription</key>
<string>Accessing your stored share</string>
```
{% endhint %}

#### Install the package in your React Native project.

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add @portal-hq/keychain
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save @portal-hq/keychain
```
{% endtab %}
{% endtabs %}

#### Import the package into the component where you initialize the Portal class

```typescript
import Portal from '@portal-hq/core'
import Keychain from '@portal-hq/keychain'
```

#### Register your storage adapter and initialize the Portal class

```typescript
const keychain = new Keychain()
const portal = new Portal({
  // ...your other config options
  keychain,
})
```



### Dependency linking

Because this package uses the `react-native-keychain` package (which contains native modules) there is some additional linking required to make it work with your React Native project.

Explicitly install the `react-native-keychain` package in your project.

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add react-native-keychain
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save react-native-keychain
```
{% endtab %}
{% endtabs %}
