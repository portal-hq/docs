# @portal-hq/mobile-key-values

The `@portal-hq/mobile-key-values` package contains a storage adapter intended for use with the `@portal-hq/core` package.

This storage adapter allows end users to save MPC shares to their local device keychain using `react-native-mmkv`.

### Basic usage

#### Install the package in your React Native project.

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add @portal-hq/mobile-key-values
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save @portal-hq/mobile-key-values
```
{% endtab %}
{% endtabs %}

#### Import the package into the component where you initialize the Portal class

```jsx
import Portal from '@portal-hq/core'
import Keychain from '@portal-hq/mobile-key-values'
```

#### Register your storage adapter and initialize the Portal class

```typescript
const keychain = new Keychain() // Optionally accepts an MMKV configuration object
const portal = new Portal({
  // ...your other config options
  keychain,
})
```

### Dependency linking

Because this package uses the `react-native-mmkv` package (which contains native modules) there is some additional linking required to make it work with your React Native project.

Explicitly install the `react-native-mmkv` package in your project.

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add react-native-mmkv
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save react-native-mmkv
```
{% endtab %}
{% endtabs %}
