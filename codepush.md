# How to use codepush

### Register GitHub at appcenter
Go to the https://appcenter.ms  
And register the GitHub account.

### Register the application at appcenter

We need to register the app each OS(ios and android).  
Enter below code and you can change the optional name at `test-android` and `test-ios`.
```
npm install -g code-push-cli
appcenter login
appcenter apps create -d test-android -o Android -p React-Native
appcenter apps create -d test-ios -o iOS -p React-Native
```

### Install the npm package
Enter the below code.  
About deployment key, you can check(appcenter) at `Dashboard -> Distribute -> CodePush -> Create standard deployment -> tool mark(top right) -> copy Staging key`  
Official document recommned to ues Staging key not Deployment key.  
https://docs.microsoft.com/en-us/appcenter/distribution/codepush/react-native#getting-started
```
npm install --save react-native-code-push
react-native link react-native-code-push
? What is your CodePush deployment key for Android (hit <ENTER> to ignore) CLlPV
KvMjG91
Running ios postlink script
? What is your CodePush deployment key for iOS (hit <ENTER> to ignore) 8aj3JsHVW
kYLi
```

### Implement the appcenter API at root.js
Open the `root.js`(or `index.js`) and add the below code.  
You need to change the `App` depends on the class name.
```
import codePush from 'react-native-code-push';

const codePushOptions = {
  checkFrequency: codePush.CheckFrequency.ON_APP_START,
};
export default codePush(codePushOptions)(App);
```

Push the code at appcenter
You can check the app name at appcenter by using `appcenter apps list`.
After checking, you can push the code at appcenter.

```
appcenter apps list
  hogehoge-gmail.com/test-android
  hogehoge-gmail.com/test-ios

appcenter codepush release-react -a hogehoge-gmail.com/test-android -d Staging
appcenter codepush release-react -a hogehoge-gmail.com/test-ios -d Staging
```

If error happens like below, try by using the flag `--disable-duplicate-release-error`  
Ref: https://github.com/Microsoft/code-push/issues/255#issuecomment-419273021
```
Error: The uploaded package was not released because it is identical to the contents of the specified deployment's current release.

appcenter codepush release-react -a re1college.box-gmail.com/test-android -d Staging --disable-duplicate-release-error
```

After pushing correctly, open your app(or close and reopen) and you can see the result that change the JS code automatically.