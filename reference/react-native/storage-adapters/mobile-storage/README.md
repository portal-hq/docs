# Mobile storage

Portal's MPC functionality stores user's MPC signing shares on-device. In order to enable this functionality, your must register a mobile storage adapter with your Portal instance.

You can use one of the following mobile storage adapters within your app:&#x20;

* [@portal-hq/keychain](portal-hq-keychain.md) - uses `react-native-keychain`
* [@portal-hq/mobile-key-values](portal-hq-mobile-key-values.md) - uses `react-native-mmkv`
