# TVBoxOSC

![Build](https://shields.io/github/actions/workflow/status/o0HalfLife0o/TVBoxOSC/test.yml?branch=master&logo=github&label=Build)
[![Channel](https://img.shields.io/badge/Follow-Telegram-blue.svg?logo=telegram)](https://t.me/TVBoxOSC)
[![Download](https://img.shields.io/github/v/release/o0HalfLife0o/TVBoxOSC?color=orange&logoColor=orange&label=Download&logo=DocuSign)](https://github.com/o0HalfLife0o/TVBoxOSC/releases/latest) 
[![Total](https://shields.io/github/downloads/o0HalfLife0o/TVBoxOSC/total?logo=Bookmeter&label=Counts&logoColor=yellow&color=yellow)](https://github.com/o0HalfLife0o/TVBoxOSC/releases)

## Credits
This repo relies on the following third-party projects:
- [CatVodTVOfficial/TVBoxOSC](https://github.com/CatVodTVOfficial/TVBoxOSC)
- [q215613905/TVBoxOS](https://github.com/q215613905/TVBoxOS) (Updated: 74ef7855b38bbe87b95af45345acacce809f11bd)
- [takagen99/Box](https://github.com/takagen99/Box) (Updated: 5bfb7abb754ab6cf4fa98c50521a03619f5aa69f)


### windows上构建

- keytool工具进行签名

```shell
# 进入app目录生成签名signkey.jks

# 下载java sdk,比如zulu11.56.19
# 会生成signkey.jks,记住-alias的值,比如upload
D:\work\zulu11.56.19-ca-jdk11.0.15-win_x64\bin\keytool.exe -genkey -v -keystore signkey.jks -storetype JKS -keyalg RSA -keysize 2048 -validity 10000 -alias upload
# 输入2次密码,记住,比如123456和654321

# 配置到app\build.gradle文件,与defaultConfig平级

signingConfigs {
    release {
        keyAlias "upload"
        keyPassword "654321"
        storeFile file("signkey.jks")
        storePassword "123456"
    }
}
buildTypes {
    release {
        signingConfig signingConfigs.release
    }
}


# 生成位置:
# app\build\outputs\apk\release\app-release.apk
```

- 环境安装/构建apk

```shell
# 下载Android Studio的commandlinetools工具而不用下载Android Studio
https://dl.google.com/android/repository/commandlinetools-win-9477386_latest.zip
# 解压之后使用sdkmanager.bat进行命令行操作
## --sdk_root指定sdk的下载目录,--list查看线上的包
D:\a\cmdline-tools\bin\sdkmanager.bat --sdk_root=D:\a\cmdline-tools\sdk --list
# 安装包(为什么是android-30,文件app\build.gradle里面的compileSdkVersion)
D:\a\cmdline-tools\bin\sdkmanager.bat --sdk_root=D:\a\cmdline-tools\sdk --install "platforms;android-30"

# 编译指定 ANDROID_SDK_ROOT 环境变量来指定sdk目录,或者项目根目录新增local.properties文件
D:\work\TVBoxOS\local.properties
## local.properties文件增加sdk的目录配置
sdk.dir=D:\\a\\cmdline-tools\\sdk

# 构建
./gradlew assemblerelease --build-cache --parallel --daemon --warning-mode all
# apk生成位置,没有签名的
# app\build\outputs\apk\release\app-release-unsigned.apk
```

- 国内依赖源

```
// 修改 build.gradle文件,两个位置
// 加上--refresh-dependencies参数清除依赖缓存
// ./gradlew assemblerelease --refresh-dependencies --build-cache --parallel --daemon --warning-mode all

// gradlePluginPortal
maven {
    url "https://maven.aliyun.com/repository/gradle-plugin"
}
// https://developer.aliyun.com/article/754038
// central仓和jcenter仓的聚合仓
maven {
    url 'https://maven.aliyun.com/repository/public/'
}
mavenLocal()
mavenCentral()
// walk_shared_librar 包
maven {
    url 'https://download.01.org/crosswalk/releases/crosswalk/android/maven2'
}
// google
maven { url "https://maven.aliyun.com/repository/google" }
// jitpack
maven { url "https://jitpack.io" }
```

- linux build in docker

```shell
docker run -itd -v /home/jcleng/desk/work/androidsdk:/home/jcleng/desk/work/androidsdk \
-v /home/jcleng/desk/work/zulu11.62.17-ca-jdk11.0.18-linux_x64:/home/jcleng/desk/work/zulu11.62.17-ca-jdk11.0.18-linux_x64 \
-v /home/jcleng/desk/work/TVBoxOS:/home/jcleng/desk/work/TVBoxOS \
--name=ubuntu_android ubuntu:20.04

export JAVA_HOME=/home/jcleng/desk/work/zulu11.62.17-ca-jdk11.0.18-linux_x64
./gradlew assemblerelease --refresh-dependencies --build-cache --parallel --daemon --warning-mode all
```
