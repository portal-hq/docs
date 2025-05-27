# @portal-hq/gdrive-storage

## Portal Google Drive backup storage

The `@portal-hq/gdrive-storage` package contains a storage adapter intended for use with the `@portal-hq/core` package.

This storage adapter allows end users to save backup MPC shares to their Google Drive.

### Basic usage

#### Install the package in your React Native project.

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add @portal-hq/gdrive-storage
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save @portal-hq/gdrive-storage
```
{% endtab %}
{% endtabs %}

#### Import the package into the component where you initialize the Portal class

```typescript
import Portal from '@portal-hq/core'
import GoogleStorage from '@portal-hq/google-storage'
```

#### Register your storage adapter and initialize the Portal class

```typescript
const storage = new GoogleStorage({
  // Required for Android
  androidClientId: 'YOUR_ANDROID_CLIENT_ID',
  // Required for iOS
  iosClientId: 'YOUR_IOS_CLIENT_ID',
  // Optional
  folder: 'YOUR_CUSTOM_STORAGE_LOCATION',
})
const portal = new Portal({
  // ...your other config options
  backup: {
    gdrive: storage,
  },
})
```

### **Create a Google App**

To enable Google Drive backups you will need to create a Google Project within your organization that will act as the OAuth consumer.

* Create a new Google project [in the Google Cloud Console](https://console.cloud.google.com/)
* On the [APIs & Services](https://console.cloud.google.com/apis) page click `ENABLE APIS AND SERVICES`
* Search for `Google Drive API`, select it, and click `Enable`
*   Click on OAuth consent screen

    * For user type select internal.&#x20;
    * Fill out the relevant information.

    _Skip scopes as we configure that within our sdk._&#x20;
* Click on Credentials in the left menu and select `+ Create Credentials`&#x20;

{% tabs %}
{% tab title="Android" %}
- Select `OAuth client ID` and choose `Android` from the `Application Type` list
- Fill in the form as instructed, with `Name`, `Package name`, and `SHA-1` from your Keystore. &#x20;
  * _Note that when testing you'll want to use your debug.keystore at /android/app/debug.keystore; However, you'll need to switch this value when pushing it to the store._
  * _Find the Sha-1 fingerprint with this command from the root project folder:_ `keytool -keystore ./app/debug.keystore -list -v`
- Once you have successfully created your `Android Client ID` , download the JSON, and rename it to be`google-service.json`
- Move the downloaded file into the `/android/app` directory.
- Now, go back to the `Credentials` screen from the previous step and select `Web Application.`
- Input the `name`. Then click create and copy the value from `Your Client ID`
- This value is what you will pass into the `PortalOptions` config when instantiating your `portal` class.
- Add emails for internal testing until you've published your app for external use.
{% endtab %}

{% tab title="iOS" %}
* Select `OAuth client ID` and choose `iOS` from the `Application Type` list
* Get your `Bundle ID` from the react-native / iOS project
* Once you've successfully created your iOS client ID, download the .plist config and copy the `REVERSED_CLIENT_ID` to the URL Scheme section of your `info.plist` either in xcode or your IDE:

{% code title="info.plist" overflow="wrap" lineNumbers="true" %}
```
...
	<key>CFBundleURLTypes</key>
	<array>
		<dict>
			<key>CFBundleTypeRole</key>
			<string>Editor</string>
			<key>CFBundleURLSchemes</key>
			<array>
				<string>REVERSE_CLIENT_ID</string>
			</array>
		</dict>
	</array>
...
```
{% endcode %}

* Rename the downloaded `.plist` file to `GoogleService-Info.plist` and save that to your `/ios` directory&#x20;
* Copy and paste the `Client ID` from the `GoogleService-Info.plist`into the `PortalOptions` config object when instantiating your `portal` class
{% endtab %}
{% endtabs %}
