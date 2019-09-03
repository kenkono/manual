# How to use fastlane

## ios

Note:Check working by the emulator correctly before doing fastlane. 

```
sudo gem install bundler
cd ios
bundle init
```

Go to the `ios/Gemfile` and add below sentence and enter the command

```
gem "fastlane"
```

```
bundle update
bundle exec fastlane produce
```

Answer below question

```
Your Apple ID Username:hogehoge@gmail.com
Multiple teams found on the Developer Portal, please enter the number of the team you want to use:
1) J56CD9XRYW "VANANAZ SYSTEMS INC." (Company/Organization)
2) ZSGPZXXA2Q "YourStand, inc." (Company/Organization)
1
App Identifier (Bundle ID, e.g. com.krausefx.app): org.reactjs.native.example.ReactNativePlatformVananaz
-> check the general at ios/ReactNativePlatform.xcodeproj
App Name: vananazTestApp
-> you can choose any name
1) "VANANAZ SYSTEMS INC." (118381617)
2) "YourStand, inc." (119185798)
1
```

Now you can see the app project at MyApp from Apple developer

```
bundle exec fastlane init
```
Answer below question

```
1. 📸  Automate screenshots
2. 👩‍✈️  Automate beta distribution to TestFlight
3. 🚀  Automate App Store distribution
4. 🛠  Manual setup - manually setup your project to automate your tasks
? 3
1. ReactNativePlatform-tvOS
2. ReactNativePlatform
? 2
[18:35:26]: Apple ID Username:
hogehoge@gmail.com
Multiple App Store Connect teams found, please enter the number of the team you want to use:
1) "VANANAZ SYSTEMS INC." (118381617)
2) "YourStand, inc." (119185798)
1
Multiple teams found on the Developer Portal, please enter the number of the team you want to use:
1) J56CD9XRYW "VANANAZ SYSTEMS INC." (Company/Organization)
2) ZSGPZXXA2Q "YourStand, inc." (Company/Organization)
1
[18:39:54]: Would you like fastlane to manage your app's metadata? (y/n)
y
```

Add `ios/fastlane/Deliverfile`

```
app_rating_config_path "./fastlane/metadata/ratings_config.json"
```

Make file `ios/fastlane/metadata/ratings_config.json`
And edit below

```
{
    "CARTOON_FANTASY_VIOLENCE": 0,
    "REALISTIC_VIOLENCE": 0,
    "PROLONGED_GRAPHIC_SADISTIC_REALISTIC_VIOLENCE": 0,
    "PROFANITY_CRUDE_HUMOR": 0,
    "MATURE_SUGGESTIVE": 0,
    "HORROR": 0,
    "MEDICAL_TREATMENT_INFO": 0,
    "ALCOHOL_TOBACCO_DRUGS": 0,
    "GAMBLING": 0,
    "SEXUAL_CONTENT_NUDITY": 0,
    "GRAPHIC_SEXUAL_CONTENT_NUDITY": 0,
    "UNRESTRICTED_WEB_ACCESS": 0,
    "GAMBLING_CONTESTS": 0
  }
```
edit `ios/fastlane/Fastfile`  
Note: Change the part `ReactNativePlatform` depends on project name.

```
default_platform(:ios)

platform :ios do
  desc "Push a new release build to the App Store"
  lane :release do
    increment_build_number(xcodeproj: "ReactNativePlatform.xcodeproj")
    match(type: "appstore")
    build_app(scheme: "ReactNativePlatform")
    upload_to_app_store(
      skip_waiting_for_build_processing: true
    )
    clean_build_artifacts
  end

  desc "Push a new release build to TestFlight"
  lane :beta do
    increment_build_number(xcodeproj: "ReactNativePlatform.xcodeproj")
    match(type: "appstore")
    build_app(scheme: "ReactNativePlatform")
    upload_to_testflight(
      skip_waiting_for_build_processing: true
    )
    clean_build_artifacts
  end
end
```

Create GItHub `private repository` to store certificates and provisioning profiles.

```
bundle exec fastlane match init
```

Answer below questions

```
[19:04:30]: fastlane match supports multiple storage modes, please select the one you want to use:
1. git
2. google_cloud
? 1
[19:05:12]: URL of the Git Repo:git@github.com:kenkono/private-certificate.git
-> using your private repository SSH URL
```
You can see the file `ios/fastlane/Matchfile`

```
bundle exec fastlane match development

Passphrase for Git Repo: put your github password
```

If error occur `[!] Could not create another Development certificate, reached the maximum number of available Development certificates.`  
Check with Mr.Nakazono(below command is delete command) and  
Run `bundle exec fastlane match nuke development`

```
bundle exec fastlane match appstore
```

If error occur `[!] Could not create another Distribution certificate, reached the maximum number of available Distribution certificates.`  
Check with Mr.Nakazono(below command is delete command) and  
Run `bundle exec fastlane match nuke appstore`

Open Xcode and change the general
- uncheck Automatically manage signing
- Signing(Debug) match Development ...
- Signing(Release) match Appstore ...

Change reactnativeplatformTests(Top left side icon)  

Signing(Debug)
- team: VANANAZ SYSTEM
- Signing Certificate: iOS Developer  

Signing(Release)
- team: VANANAZ SYSTEM
- Signing Certificate: iOS Distribution

No need to change because already changed??  
Select Build settings(Top right side icon)  
serch `code signing identity`
- Debug iOS Developer
- Release iOS Distribution  
Do same as reactnativeplatform(Top left side icon)

Go to `reactnariveplatform(project name)/info.plist` (left side bar)   
Right click and push add row.  
Put `ITSAppUsesNonExemptEncryption`

Go to the reactnativeplatform->general->appiconsource(Xcode).  
Fill in the icon.  
Put the screenshot at `ios/fastlane/screenshots`  
You can describe some metadata under `ios/fastlane/metadata/en-US`  

Finally put the below command at `ios` directory.

```
bundle exec fastlane beta
```

Sometime below error occures.
```
[16:09:13]: Verifying the upload via the HTML file can be disabled by either adding
[16:09:13]: `force true` to your Deliverfile or using `fastlane deliver --force`
[16:09:14]: Does the Preview on path './fastlane/Preview.html' look okay for
you? (y/n)
y
```

You can chack testflight page.  

# android
note:We need to make .apk file and deploy at Google Play Console manually at first.

# Create .apk file

```
mkdir secure
cd android/secure
sudo keytool -genkey -v -keystore my-upload-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000

sudo keytool -genkey -v -keystore kikureco.keystore -alias kikureco -keyalg RSA -keysize 2048 -validity 10000
```
Rt5fSuPkN33NAfhD

Edit `build.gradle`.  
We have three choices.  
1. Use reference file

```build.gradle
android {
    ...
    defaultConfig { ... }
    signingConfigs {
        release {
            if (project.hasProperty('MYAPP_UPLOAD_STORE_FILE')) {
                storeFile file(MYAPP_UPLOAD_STORE_FILE)
                storePassword MYAPP_UPLOAD_STORE_PASSWORD
                keyAlias MYAPP_UPLOAD_KEY_ALIAS
                keyPassword MYAPP_UPLOAD_KEY_PASSWORD
            }
        }
    }
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
...
```

Edit `android/gradle.properties`
```gradle.properties
MYAPP_UPLOAD_STORE_FILE=my-upload-key.keystore
MYAPP_UPLOAD_KEY_ALIAS=my-key-alias
MYAPP_UPLOAD_STORE_PASSWORD=*****
MYAPP_UPLOAD_KEY_PASSWORD=*****
```

2. Use env file(For using `Bitrise`, need to select this choice)
```
android {
    ...
    defaultConfig { ... }
    signingConfigs {
        release {
            if (project.hasProperty('MYAPP_UPLOAD_STORE_FILE')) {
                storeFile file(System.getenv("MYAPP_UPLOAD_STORE_FILE"))
                storePassword System.getenv("MYAPP_UPLOAD_STORE_PASSWORD")
                keyAlias System.getenv("MYAPP_UPLOAD_KEY_ALIAS")
                keyPassword System.getenv("MYAPP_UPLOAD_KEY_PASSWORD")
            }
        }
    }
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
...
```

Put environment valuable
```
export MYAPP_UPLOAD_STORE_FILE="/Users/konoken/vananaz/react-native-platform/android/secure/my-upload-key.keystore"
export MYAPP_UPLOAD_KEY_ALIAS="my-key-alias"
export MYAPP_UPLOAD_STORE_PASSWORD="*****"
export MYAPP_UPLOAD_KEY_PASSWORD="*****"
```

Check whether env is working correctly.
```
echo $MYAPP_UPLOAD_STORE_FILE
echo $MYAPP_UPLOAD_KEY_ALIAS
echo $MYAPP_UPLOAD_STORE_PASSWORD
echo $MYAPP_UPLOAD_KEY_PASSWORD
```

3. Magic number
```
    signingConfigs {
        release {
                storeFile file("../secure/my-upload-key.keystore")
                storePassword "vananaz"
                keyAlias "my-key-alias"
                keyPassword "vananaz"
        }
    }
```
When upload apk at google play console  
Error API level at least 28  
edit `android/build.gradle`
```
buildscript {
    ext {
        targetSdkVersion = 28
```
 
Error 64bit  
edit `android/app/build.gradle`  
ref:https://developer.android.com/distribute/best-practices/develop/64-bit#building_with_android_studio_or_gradle

```
    defaultConfig {
        applicationId "jp.co.hogehoge"
        minSdkVersion rootProject.ext.minSdkVersion
        targetSdkVersion rootProject.ext.targetSdkVersion
        versionCode 1
        versionName "1.0"
        ndk {
            abiFilters 'armeabi-v7a','arm64-v8a','x86','x86_64'
        }
    }
``` 

If you update ndk version and try to make `.apk` file next time, below error occures sometime.
```
error: invalid file path '/Users/konoken/kikutan-mobile/node_modules/react-native-gesture-handler/android/build/intermediates/manifests/aapt/release/AndroidManifest.xml'.
```
ref:https://stackoverflow.com/a/21795177  
Go to your android studio code and `Build -> Clean Project` then `File -> Invalidate Caches/Restart`.  
Then try again.


バージョンの上げ方  
https://high-programmer.com/2019/02/06/update-android-app-by-google-play/ 

### Get .apk file
```
cd android
./gradlew assembleRelease
```

Note: Keep your `my-upload-key.keystore`(Do not be lost) because you should use this one next update.  

You can see .apk file at `android/app/build/outputs/apk/release`  

Then go to the Google play console and CREATE APPLICATION with using this .apk file.  

If error happen invalid certificate, please check `signingConfig signingConfigs.release` is valid

### (Note)Check the fingerprint at .apk file
```
keytool -list -keystore [file name]
ex:
keytool -list -keystore my-upload-key.keystore
```

# Get API at Google Play Console
Go to the Google Play Console

Settings -> Developer account -> API access  
Service accounts -> CREATE SERVICE ACCOUNT  
Google API console -> + CREATE SERVICE ACCOUNT

service account name: hogehoge  
service account permisson: Projet -> Owner(full access)  
optional: blank  

Actions -> create key -> json

After done above, you can get `***.json`  
make folder `android/secure` and put json file under that directory.

Back to Google Play Console and Settings -> Developer account -> API access  
enter GRANT ACCESS  
Role: administrator

# Initialize fastlane

```
sudo gem install bundler
cd android
bundle init
```

Go to the `android/Gemfile` and add below sentence and enter the command

```
gem "fastlane"
```

```
bundle update
```

Next, initialize fastlane  
Below is the answer you recevied question.  

Package name -> put `applicationId` at `android/app/build.gradle`  
Path to the json -> put `secure/***.json`  
meta -> y

```
bundle exec fastlane init
```

Add plugin to update version number when execite fastlane each time.  
After done, automatically add `Gemfile`

```
bundle exec fastlane add_plugin increment_version_code
```

```Gemfile
plugins_path = File.join(File.dirname(__FILE__), 'fastlane', 'Pluginfile')
eval_gemfile(plugins_path) if File.exist?(plugins_path)
```

Edit `android/fastlane/Fastfile`

```Fastfile
default_platform(:android)

platform :android do

  desc "submit internal release to Google Play Store"
  lane :internal do
    # update verion at build.gradle
    increment_version_code(
      gradle_file_path: "./app/build.gradle"
    )
    # make apk file
    gradle(task: "assembleRelease")
    # find the apk file
    supply(
      track: "internal",
      apk: "#{lane_context[SharedValues:: GRADLE_APK_OUTPUT_PATH]}"
    )
  end

  desc "submit alpha release to Google Play Store"
  lane :alpha do
    increment_version_code(
      gradle_file_path: "./app/build.gradle"
    )
    gradle(task: "assembleRelease")
    supply(
      track: "alpha",
      apk: "#{lane_context[SharedValues:: GRADLE_APK_OUTPUT_PATH]}"
    )
  end

  desc "submit beta release to Google Play Store"
  lane :beta do
    increment_version_code(
      gradle_file_path: "./app/build.gradle"
    )
    gradle(task: "assembleRelease")
    supply(
      track: "beta",
      apk: "#{lane_context[SharedValues:: GRADLE_APK_OUTPUT_PATH]}"
    )
  end

  desc "submit production release to Google Play Store"
  lane :production do
    increment_version_code(
      gradle_file_path: "./app/build.gradle"
    )
    gradle(task: "assembleRelease")
    supply(
      track: "production",
      apk: "#{lane_context[SharedValues:: GRADLE_APK_OUTPUT_PATH]}"
    )
  end

end
```

Preparement is done.  
You can choose deploy mode.  
For example, if you would like to deploy at `internal`, enter below code.

```
bundle exec fastlane internal
```
Generate `.apk` file automatically and deploy to Google Play Console.  
And automatically update version number also at `android/app/build.gradle`

# Bitrise(android) 
Note:Put the ssh public key at user settings not each repository.
### Increment version settings

edit `android/app/build.gradle` to use Bitrise commit number.  
Ref:https://medium.com/@AndreSand/adding-bitrise-build-number-to-android-app-version-code-7ad3be41908a  
https://devcenter.bitrise.io/builds/build-numbering-and-app-versioning/
```
    defaultConfig {
        applicationId "com.reactnativeplatformBitrise"
        minSdkVersion rootProject.ext.minSdkVersion
        targetSdkVersion rootProject.ext.targetSdkVersion

        Integer buildNumb = System.getenv("BITRISE_BUILD_NUMBER") as Integer
        versionCode buildNumb ?: 1 
        
        versionName "1.0"
        ndk {
            abiFilters 'armeabi-v7a','arm64-v8a','x86','x86_64'
        }
    }
```

And remove increment function from `android/fastlane/Fastfile` because we use Bitrise working number and increment this number automatically.

```
  desc "submit internal release to Google Play Store"
  lane :internal do

    # remove below increment function 
    # increment_version_code(
    #   gradle_file_path: "./app/build.gradle"
    # )

    # make apk file
    gradle(task: "assembleRelease")
    # find the apk file
    supply(
      track: "internal",
      apk: "#{lane_context[SharedValues:: GRADLE_APK_OUTPUT_PATH]}"
    )
```

### Env Vars
Add below environment variables.

```
$FASTLANE_WORK_DIR = android
$FASTLANE_LANE = android internal
$GITHUB_USER_NAME = hogehoge
$GITHUB_USER_EMAIL = hogehoge@gmail.com
```

### Secrets
Add below variables.

```
$MYAPP_UPLOAD_STORE_FILE = $BITRISE_SOURCE_DIR/android/secure/my-upload-key.keystore
$MYAPP_UPLOAD_STORE_PASSWORD = $BITRISEIO_ANDROID_KEYSTORE_PASSWORD
$MYAPP_UPLOAD_KEY_ALIAS = $BITRISEIO_ANDROID_KEYSTORE_ALIAS
$MYAPP_UPLOAD_KEY_PASSWORD = $BITRISEIO_ANDROID_KEYSTORE_PRIVATE_KEY_PASSWORD
$GOOGLE_JSON_SECRET_FILE = $BITRISE_SOURCE_DIR/android/secure/api-4654920470886275998-753658-e2d2deefe64a.json
```

### Code Signing

Upload keystore file at `ANDROID KEYSTORE FILE
`
And put your keystore alias and password.  

Upload google play console api file at `GENERIC FILE STORAGE`  
File Storage ID is `JSON_SECRET`

### Workflows

Add below flow
- Activate SSH key  
default
- Script
```
#!/usr/bin/env bash
# fail if any commands fails
set -e
# debug log
set -x

# Add Github to known hosts
ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts

# Configure Git User
git config --global user.name $GITHUB_USER_NAME
git config --global user.email $GITHUB_USER_EMAIL 
```
- Git Clone Repository  
default
- File Downloader  
```
Download source url = $BITRISEIO_ANDROID_KEYSTORE_URL
Download destination path = $MYAPP_UPLOAD_STORE_FILE
```
- File Downloader  
```
Download source url = $BITRISEIO_JSON_SECRET_URL
Download destination path = $GOOGLE_JSON_SECRET_FILE
```
- Install React Native  
default
- Run npm command
```
Working directory = $BITRISE_SOURCE_DIR
The `npm` command with arguments to run = install
```
- fastlane
```
fastlane lane = $FASTLANE_LANE
Working directory = $FASTLANE_WORK_DIR
```
- Deploy to Bitrise.io  
default

### Triggers
Select which branch we would like to hool and which WORKFLOW we would like to act.



# Bitrise(ios)
Note:Put the ssh public key at user settings not each repository.
### Increment verions setting

Remove increment function from `ios/fastlane/Fastfile` because we use Bitrise working number and increment this number automatically.  
Ref: https://tech.gunosy.io/entry/ios_build_number_with_fastlane_or_bitrise

```
  desc "Push a new release build to the App Store"
  lane :release do
    # remove increment function
    # increment_build_number(xcodeproj: "ReactNativePlatform.xcodeproj")

    match(type: "appstore")
    build_app(scheme: "ReactNativePlatform")
    upload_to_app_store(
      skip_waiting_for_build_processing: true
    )
    clean_build_artifacts
  end
```

### Env Vars
Add below environment variables.

```
$FASTLANE_XCODE_LIST_TIMEOUT = 120
$FASTLANE_WORK_DIR = ios
$FASTLANE_LANE = ios beta
$GITHUB_USER_NAME = hogehoge
$GITHUB_USER_EMAIL = hogehoge@gmail.com
```

### Secrets
Add below variables.

```
$FASTLANE_USER = Email about appleID
$FASTLANE_PASSWORD = Password about appleID
$MATCH_PASSWORD = Password about GitHub
$INFO_PLIST_PATH = ios/ReactNativePlatform(application name)/Info.plist
```

### Workflows

Add below flow
- Activate SSH key  
default
- Script
```
#!/usr/bin/env bash
# fail if any commands fails
set -e
# debug log
set -x

# Add Github to known hosts
ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts

# Configure Git User
git config --global user.name $GITHUB_USER_NAME
git config --global user.email $GITHUB_USER_EMAIL 
```
- Git Clone Repository  
default
- Install React Native  
default
- Run npm command
```
Working directory = $BITRISE_SOURCE_DIR
The `npm` command with arguments to run = install
```
- Set Xcode Project Build Number
```
Info.plist file path = $INFO_PLIST_PATH
Build Number = $BITRISE_BUILD_NUMBER
```
- fastlane
```
fastlane lane = $FASTLANE_LANE
Working directory = $FASTLANE_WORK_DIR
```
- Deploy to Bitrise.io  
default

### Triggers
Select which branch we would like to hook and which WORKFLOW we would like to act.

# Bitrise(work ios and android at the same time)

### Workflows

- Activate SSH key (RSA private key)  
default  

- Git Clone Repository  
default  

- Script

```
#!/usr/bin/env bash
# fail if any commands fails
set -e
# debug log
set -x

bitrise run common --config bitrise.yml
bitrise run ios --config bitrise.yml &
bitrise run android --config bitrise.yml &
wait
```
### Env Vars
Add below environment variables.

```
# Common
$GITHUB_USER_NAME = hogehoge
$GITHUB_USER_EMAIL = hogehoge@gmail.com
# For android
$FASTLANE_WORK_DIR_ANDROID = android
$FASTLANE_LANE_ANDROID = android internal
# For ios
$FASTLANE_XCODE_LIST_TIMEOUT = 120
$FASTLANE_WORK_DIR_IOS = ios
$FASTLANE_LANE_IOS = ios beta
```

### Secrets
Add below variables.  
Change the correct name of `json file`.
```
# For android
$MYAPP_UPLOAD_STORE_FILE = $BITRISE_SOURCE_DIR/android/secure/my-upload-key.keystore
$MYAPP_UPLOAD_STORE_PASSWORD = $BITRISEIO_ANDROID_KEYSTORE_PASSWORD
$MYAPP_UPLOAD_KEY_ALIAS = $BITRISEIO_ANDROID_KEYSTORE_ALIAS
$MYAPP_UPLOAD_KEY_PASSWORD = $BITRISEIO_ANDROID_KEYSTORE_PRIVATE_KEY_PASSWORD
$GOOGLE_JSON_SECRET_FILE = $BITRISE_SOURCE_DIR/android/secure/api-4654920470886275998-753658-e2d2deefe64a.json
# For ios
$FASTLANE_USER = Email about appleID
$FASTLANE_PASSWORD = Password about appleID
$MATCH_PASSWORD = Password about GitHub
$INFO_PLIST_PATH = ios/ReactNativePlatform(application name)/Info.plist
```

### Code Signing

Upload keystore file at `ANDROID KEYSTORE FILE
`
And put your keystore alias and password.  

Upload google play console api file at `GENERIC FILE STORAGE`  
File Storage ID is `JSON_SECRET`


### bitrise.yml(Put the root directory)
```
---
format_version: '8'
default_step_lib_source: https://github.com/bitrise-io/bitrise-steplib.git
project_type: react-native
workflows:
  common:
    steps:
    - script@1.1.5:
        title: Git Confiure
        inputs:
        - content: "#!/usr/bin/env bash\n# fail if any commands fails\nset -e\n# debug
            log\nset -x\n\n# Add Github to known hosts\nssh-keyscan -t rsa github.com
            >> ~/.ssh/known_hosts\n\n# Configure Git User\ngit config --global user.name
            $GITHUB_USER_NAME\ngit config --global user.email $GITHUB_USER_EMAIL "
  android:
    steps:
    - install-react-native@0.9.1: {}
    - npm@1.1.0:
        inputs:
        - command: install
    - file-downloader@1.0.1:
        title: File Downloader for Keystore
        inputs:
        - destination: "$MYAPP_UPLOAD_STORE_FILE"
        - source: "$BITRISEIO_ANDROID_KEYSTORE_URL"
    - file-downloader@1.0.1:
        title: File Downloader for JSON file
        inputs:
        - source: "$BITRISEIO_JSON_SECRET_URL"
        - destination: "$GOOGLE_JSON_SECRET_FILE"
    - fastlane@2.5.2:
        inputs:
        - work_dir: "$FASTLANE_WORK_DIR_ANDROID"
        - lane: "$FASTLANE_LANE_ANDROID"
    - deploy-to-bitrise-io@1.7.0: {}
  ios:
    steps:
    - install-react-native@0.9.1: {}
    - npm@1.1.0:
        inputs:
        - command: install
    - set-xcode-build-number@1.0.8:
        inputs:
        - plist_path: "$INFO_PLIST_PATH"
    - fastlane@2.5.2:
        inputs:
        - work_dir: "$FASTLANE_WORK_DIR_IOS"
        - lane: "$FASTLANE_LANE_IOS"
    - deploy-to-bitrise-io@1.7.0: {}
app:
  envs:
  # android
  - opts:
      is_expand: false
    FASTLANE_WORK_DIR_ANDROID: android
  - opts:
      is_expand: false
    FASTLANE_LANE_ANDROID: android internal
  - opts:
      is_expand: false
    GITHUB_USER_NAME: hogehoge
  - opts:
      is_expand: false
    GITHUB_USER_EMAIL: hogehoge@gmail.com
  # ios
  - FASTLANE_XCODE_LIST_TIMEOUT: '120'
    opts:
      is_expand: false
  - opts:
      is_expand: false
    FASTLANE_WORK_DIR_IOS: ios
  - opts:
      is_expand: false
    FASTLANE_LANE_IOS: ios beta
```

# Ref
[Publishing to Google Play Store](https://facebook.github.io/react-native/docs/signed-apk-android)  
[Nuke](https://docs.fastlane.tools/actions/match/#nuke)

# android crash on simurator(on PC)

```
android {
    compileSdkVersion rootProject.ext.compileSdkVersion
    buildToolsVersion rootProject.ext.buildToolsVersion

    defaultConfig {
        applicationId "com.kikureco"
        minSdkVersion rootProject.ext.minSdkVersion
        targetSdkVersion rootProject.ext.targetSdkVersion
        versionCode 1
        versionName "1.0"
        ndk {
            // For making apk file
            // abiFilters 'armeabi-v7a','arm64-v8a','x86','x86_64'

            // For developing to avoid crash
            abiFilters 'armeabi-v7a','x86'

        }
    }

    // Comment out below for developing to avoid crash
    // signingConfigs {
    //     release {
    //             storeFile file(System.getenv("MYAPP_UPLOAD_STORE_FILE"))
    //             storePassword System.getenv("MYAPP_UPLOAD_STORE_PASSWORD")
    //             keyAlias System.getenv("MYAPP_UPLOAD_KEY_ALIAS")
    //             keyPassword System.getenv("MYAPP_UPLOAD_KEY_PASSWORD")
    //     }
    // }

    splits {
        abi {
            reset()
            enable enableSeparateBuildPerCPUArchitecture
            universalApk false  // If true, also generate a universal APK
            include "armeabi-v7a", "x86"
        }
    }
    buildTypes {
        release {
            minifyEnabled enableProguardInReleaseBuilds
            proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
            // Comment out below for developing to avoid crash
            // signingConfig signingConfigs.release
```
