[![npm version](https://badge.fury.io/js/%40capacitor-community%2Ffacebook-login.svg)](https://badge.fury.io/js/%40capacitor-community%2Ffacebook-login)

# capacitor-facebook-login
Capacitory community plugin for Facebook Login.

## Demo
[Demo code is here.](https://github.com/rdlabo-team/capacitor-facebook-login/tree/master/demo/angular)

※ This will be move to this repository.

## Maintainers

| Maintainer | GitHub  | Social | Sponsoring Company |
| --- | --- | --- | --- |
| Masahiko Sakakibara  | [rdlabo](https://github.com/rdlabo)  | [@rdlabo](https://twitter.com/rdlabo) | RELATION DESIGN LABO, GENERAL INC. ASSOCIATION |
 
Mainteinance Status: Actively Maintained

## Installation

```bash
$ npm i --save @capacitor-community/facebook-login
```

To use yarn

```bash
yarn add @capacitor-community/facebook-login
```

### If you use Capacitor 1.x
```
$ npm install --save @rdlabo/capacitor-facebook-login@1.5.0
```

## Android configuration

In file `android/app/src/main/java/**/**/MainActivity.java`, add the plugin to the initialization list:

```diff
  this.init(savedInstanceState, new ArrayList<Class<? extends Plugin>>() {{
    [...]
+   add(com.getcapacitor.community.facebooklogin.FacebookLogin.class);
    [...]
  }});
```

In file `android/app/src/main/AndroidManifest.xml`, add the following XML elements under `<manifest><application>` :

```diff
+ <meta-data android:name="com.facebook.sdk.ApplicationId"
+     android:value="@string/facebook_app_id"/>
+ 
+ <activity
+     android:name="com.facebook.FacebookActivity"
+     android:configChanges="keyboard|keyboardHidden|screenLayout|screenSize|orientation"
+     android:label="@string/app_name" />
+ 
+ <activity
+     android:name="com.facebook.CustomTabActivity"
+     android:exported="true">
+     <intent-filter>
+         <action android:name="android.intent.action.VIEW" />
+         <category android:name="android.intent.category.DEFAULT" />
+         <category android:name="android.intent.category.BROWSABLE" />
+         <data android:scheme="@string/fb_login_protocol_scheme" />
+     </intent-filter>
+ </activity>
```

In file `android/app/src/main/res/values/strings.xml` add the following lines :

```diff
+ <string name="facebook_app_id">[APP_ID]</string>
+ <string name="fb_login_protocol_scheme">fb[APP_ID]</string>
```

Don't forget to replace `[APP_ID]` by your Facebook application Id.

More information can be found here: https://developers.facebook.com/docs/facebook-login/android

## iOS configuration

In file `ios/App/App/AppDelegate.swift` add or replace the following:

```diff
+ import FacebookCore
+ import FBSDKCoreKit
  [...]
  func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
+   FBSDKCoreKit.ApplicationDelegate.shared.application(application, didFinishLaunchingWithOptions: launchOptions)
    return true
  }

  func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
-   return CAPBridge.handleOpenUrl(url, options)
+   if CAPBridge.handleOpenUrl(url, options) {
+     return FBSDKCoreKit.ApplicationDelegate.shared.application(app, open: url, options: options)
+   }
+   else{
+    return false
+   }
  }
```

Add the following in the `ios/App/App/info.plist` file:

```diff
+ <key>CFBundleURLTypes</key>
+ <array>
+   <dict>
+     <key>CFBundleURLSchemes</key>
+     <array>
+       <string>fb[APP_ID]</string>
+     </array>
+   </dict>
+ </array>
+ <key>FacebookAppID</key>
+ <string>[APP_ID]</string>
+ <key>FacebookDisplayName</key>
+ <string>Wimlov</string>
+ <key>LSApplicationQueriesSchemes</key>
+ <array>
+   <string>fbapi</string>
+   <string>fbauth2</string>
+ </array>
```

More information can be found here: https://developers.facebook.com/docs/facebook-login/ios

## Web configuration

```ts
+ window.fbAsyncInit = function() {
+   FB.init({
+     appId: '[APP_ID]',
+     cookie: true, // enable cookies to allow the server to access the session
+     xfbml: true, // parse social plugins on this page
+     version: 'v5.0' // use graph api current version
+   });
+ };

+ // Load the SDK asynchronously
+ (function(d, s, id) {
+   var js, fjs = d.getElementsByTagName(s)[0];
+   if (d.getElementById(id)) return;
+   js = d.createElement(s); js.id = id;
+   js.src = "https://connect.facebook.net/en_US/sdk.js";
+   fjs.parentNode.insertBefore(js, fjs);
+ }(document, 'script', 'facebook-jssdk'));
```

```ts
  // Init Capacitor
+ import { registerWebPlugin } from '@capacitor/core';
+ import { FacebookLogin } from '@capacitor-community/facebook-login';
  ...
+ registerWebPlugin(FacebookLogin);
```

More information can be found here: https://developers.facebook.com/docs/facebook-login/web
And you must confirm return type at https://github.com/rdlabo/capacitor-facebook-login/blob/master/src/web.ts#L55-L57
not same type for default web facebook login!


## Supported methods

| Name                  | Android | iOS | Web |
| :-------------------- | :------ | :-- | :-- |
| login                 | ✅      | ✅  | ✅ |
| logout                | ✅      | ✅  | ✅  |
| getCurrentAccessToken | ✅      | ✅  | ✅  |

## API

### Login

```ts
import { Plugins } from '@capacitor/core';
import { FacebookLoginResponse } from '@capacitor-community/facebook-login';
const { FacebookLogin } = Plugins;

const FACEBOOK_PERMISSIONS = ['email', 'user_birthday', 'user_photos', 'user_gender'];
const result = await <FacebookLoginResponse>FacebookLogin.login({ permissions: FACEBOOK_PERMISSIONS });

if (result.accessToken) {
  // Login successful.
  console.log(`Facebook access token is ${result.accessToken.token}`);
} else {
  // Cancelled by user.
}
```

### Logout

```ts
import { Plugins } from '@capacitor/core';
const { FacebookLogin } = Plugins;

await FacebookLogin.logout();
```

### CurrentAccessToken

```ts
import { Plugins } from '@capacitor/core';
import { FacebookLoginResponse } from '@capacitor-community/facebook-login';
const { FacebookLogin } = Plugins;

const result = await <FacebookLoginResponse>FacebookLogin.getCurrentAccessToken();

if (result.accessToken) {
  console.log(`Facebook access token is ${result.accessToken.token}`);
} else {
  // Not logged in.
}
```
