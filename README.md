# Push Notificationsのドキュメント
FCMを送信するdemoアプリ.
## 概要
[FlutterFireのドキュメント](https://firebase.flutter.dev/docs/messaging/overview/)
[今回参考にした動画](https://www.youtube.com/watch?v=54vgoPgB8xE)

- 実行環境
    - GoogleAnalytics
    - 設定が必要ないAndroidを使用.
    - フォアグランドだと表示できないので、バックグランドにする.
    - アプリ起動したままにして、homeに戻る.
    - FCMは、受信するのに5分ぐらいかかる?

今回使用した技術

| 使用する技術 |  Version |
|--------------|----------|
|Flutter       | 3.3.1    |
|Dart          | 2.18.0   |
|firebase_core          | ^1.24.0 |
|firebase_messaging | ^13.0.4   |
|firebase_analytics          | ^9.3.7  |
|firebase_core_platform_interface        | 4.5.1 |

-----

## アプリを作ることにした背景
Firebase Cloud Messagingを使ってみたかった!
理想は、Cloud Functionsを使用してpus通知をできるようにする。

app/build.gradleの設定
```aidl
def localProperties = new Properties()
def localPropertiesFile = rootProject.file('local.properties')
if (localPropertiesFile.exists()) {
    localPropertiesFile.withReader('UTF-8') { reader ->
        localProperties.load(reader)
    }
}

def flutterRoot = localProperties.getProperty('flutter.sdk')
if (flutterRoot == null) {
    throw new GradleException("Flutter SDK not found. Define location with flutter.sdk in the local.properties file.")
}

def flutterVersionCode = localProperties.getProperty('flutter.versionCode')
if (flutterVersionCode == null) {
    flutterVersionCode = '1'
}

def flutterVersionName = localProperties.getProperty('flutter.versionName')
if (flutterVersionName == null) {
    flutterVersionName = '1.0'
}

apply plugin: 'com.android.application'
// START: FlutterFire Configuration
apply plugin: 'com.google.gms.google-services'
// END: FlutterFire Configuration
apply plugin: 'kotlin-android'
apply from: "$flutterRoot/packages/flutter_tools/gradle/flutter.gradle"

android {
    compileSdkVersion 33 // 33を指定.
    ndkVersion flutter.ndkVersion

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }

    kotlinOptions {
        jvmTarget = '1.8'
    }

    sourceSets {
        main.java.srcDirs += 'src/main/kotlin'
    }

    defaultConfig {
        // TODO: Specify your own unique Application ID (https://developer.android.com/studio/build/application-id.html).
        applicationId "com.example.fcm_tutorial"
        // You can update the following values to match your application needs.
        // For more information, see: https://docs.flutter.dev/deployment/android#reviewing-the-build-configuration.
        minSdkVersion 19 // 19を指定.
        targetSdkVersion 33 // 33を指定.
        versionCode flutterVersionCode.toInteger()
        versionName flutterVersionName
    }

    buildTypes {
        release {
            // TODO: Add your own signing config for the release build.
            // Signing with the debug keys for now, so `flutter run --release` works.
            signingConfig signingConfigs.debug
        }
    }
}

flutter {
    source '../..'
}
// "org.jetbrains.kotlin:kotlin-stdlib-jdk7を
// "org.jetbrains.kotlin:kotlin-stdlib-jdk8に変更した。
dependencies {
    implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk8:$kotlin_version"
}
```

## Firebaseのエラーが発生!
FirebaseAppPlatform.verifyExtends(_delegate)とエラーログが出たら、pubspec.yamlに
こちらのpackageを追加する。

```
firebase_core_platform_interface: 4.5.1
```

--------
## 感想
以前やってみたときは上手くいってないと思ったが、Typoはしていなくて
Androidの端末に、FCMが届くのに５分ぐらいかかるみたいでした。