```
npm install -g code-push-cli
appcenter login
appcenter apps create -d test-android -o Android -p React-Native
appcenter apps create -d test-ios -o iOS -p React-Native
```

```
npm install --save react-native-code-push
react-native link react-native-code-push
? What is your CodePush deployment key for Android (hit <ENTER> to ignore) CLlPV
KvMjG91
Running ios postlink script
? What is your CodePush deployment key for iOS (hit <ENTER> to ignore) 8aj3JsHVW
kYLi

Go to the Dashboard -> Distribute -> CodePush -> Create standard deployment -> tool mark(top right) -> copy Staging key
```

```
open the `root.js`

import codePush from 'react-native-code-push';

const codePushOptions = {
  checkFrequency: codePush.CheckFrequency.ON_APP_START,
};
export default codePush(codePushOptions)(App);
```

```
appcenter apps list
  hogehoge-gmail.com/test-android
  hogehoge-gmail.com/test-ios
appcenter codepush release-react -a hogehoge-gmail.com/test-android -d Staging
appcenter codepush release-react -a hogehoge-gmail.com/test-ios -d Staging
```

```
Error: The uploaded package was not released because it is identical to the contents of the specified deployment's current release.

appcenter codepush release-react -a re1college.box-gmail.com/test-android -d Staging --disable-duplicate-release-error
```
https://github.com/Microsoft/code-push/issues/255#issuecomment-419273021

CHeck the appcenter