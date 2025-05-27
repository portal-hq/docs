---
description: >-
  This is to allow your app to have access to store files in your users google
  drive.
---

# GDrive

## Create a google app

To enable Google Drive backups you will need to create a Google Project within your organization that will act as the OAuth consumer.

* Create a new Google project [in the Google Cloud Console](https://console.cloud.google.com/)
* On the [APIs & Services](https://console.cloud.google.com/apis) page click `ENABLE APIS AND SERVICES`
* Search for `Google Drive API`, select it, and click `Enable`
* Click on OAuth consent screen
  * For user type select internal.&#x20;
  * Fill out the relevant information.
* Our SDK requires these scopes

```
DRIVE_FILE: "https://www.googleapis.com/auth/drive.file"
DRIVE_APPFOLDER: "https://www.googleapis.com/auth/drive.appdata"
```

* Click on Credentials in the left menu and select `+ Create Credentials`&#x20;

{% hint style="warning" %}
Before going to production, you'll need to verify your application and update your `User type` to `External` in order to allow external users to authenticate with Google.
{% endhint %}

{% tabs %}
{% tab title="Android" %}
* &#x20;Select `OAuth client ID` and choose `Android` from the `Application Type` list
* Fill in the form as instructed, with `Name`, `Package name`, and `SHA-1` from your Keystore. &#x20;
  * _Note that when testing you'll want to use your debug.keystore at /android/app/debug.keystore; However, you'll need to switch this value when pushing it to the store._
  * _Find the Sha-1 fingerprint with this command from the root project folder:_ `keytool -keystore ./app/debug.keystore -list -v`
* Click on Credentials in the left menu, select `+ Create Credentials` again, and select `Oauth client ID` again. Then select `Web Application` as the Application type.
* Input the `Name`. Then click create and copy the value from `Your Client ID`.
* This value is what you will pass into the `PortalOptions` config when instantiating your `Portal` class.
* **Important:** Add emails for internal testing until you've published your app for external use.
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

### Useful Google Resources&#x20;

Should I set scopes in the OAuth Consent Screen?

* Our backup method does not require any sensitive or restrictive scopes as defined by google; however, setting scopes for your application will limit the functionality to only what is required from the permission if the temporary auth token was to get compromised. It is [recommended by google ](https://developers.google.com/identity/protocols/oauth2/production-readiness/policy-compliance?authuser=1#sensitive-restricted-scope-verification)to register all scopes

Should I get google approval?

* You do not _need_ to in order to publish your app because we don't require sensitive or restrictive scopes; however, if you would like brand verification you will need to get google permissions. Those times vary from a couple days to two weeks. [_Google docs_](https://developers.google.com/identity/protocols/oauth2/production-readiness/brand-verification?authuser=1)

