---
layout: post
title: 안드로이드 APK 파일 이름 설정하기
excerpt : "다른 포지션의 팀원들과 효율적인 APK 공유를 위한 네이밍하기"
date:   2019-06-24
tags: [android, gradle, tip]
comments: true
---
# 안드로이드 APK 파일 이름 설정하기
<br><br>


---

다른 사람들과 협업을 하면서 내가 가장 중요하게 생각하는 것은 '소통 비용(Communication Cost)'를 얼마나 줄일 수 있는가이다. 그렇기 때문에 개발 환경을 다른 팀과 협업하기 좋을 상태로 세팅 하는 것을 정말 좋아한다. 소소한 것부터...! 해외 출시 프로젝트를 한국계 한국인인 내가 혼자... 쓸쓸히... 진행하면서 편리하게 이용했던 소소한 설정에 관해서 작성하고자 한다. 그 중에 오늘은 다른 팀과의 협업을 위해 설정을 해두면 아주아주 좋은 APK 파일 이름 설정에 관한 포스팅이다. (분명 나중에 프로젝트 할 때 또 '아.. 그거 어떻게 했었지' 하면서 구글링 할 게 분명...)
<br><br><br>
## apk 파일 네이밍 설정이 필요한 이유?

---

우리 회사에서는 QA팀이나 내부 직원들에게 APK 파일을 공유할 때 AWS S3 Bucket을 이용해서 공유를 했다. 개발자 편한대로 'app-release.apk'로 올린다면 '이... 버전이 그 버전인가...?🤔 ' 하며 내 자신을 의심하게 된다. 또, 안드로이드 스튜디오에서 Apk 파일을 generate 할 때 늘 같은 이름으로 생성되기 때문에 만약 두 가지 버전을 전달해 테스트를 하고자 했다면... 이미 그 전에 뽑은 Apk 파일은 없어져 있을 것이다. 그렇기 때문에 자동으로 이름을 바꿔서 파일을 생성할 수 있도록 Gradle 세팅을 해주는 것이 좋다.
<br><br><br>
## apk 파일명이 보기 쉽게 추출되도록 Gradle file 수정하기

---

    defaultConfig {
            applicationId "프로젝트 패키지명"
            vectorDrawables.useSupportLibrary = true
            multiDexEnabled true
            minSdkVersion 17
            targetSdkVersion 26
            versionCode 9  // 버전 코드
            versionName "1.0.4"  //버전 이름
            testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
        }

그래들 파일을 보면 `defautConfig` 블럭이 있다. 버전 코드와 버전 이름에 대한 정보를 가지고 있는데, 이 코드와 이름을 앱 뒤에 붙여서 상당히 편한 APK 버전관리를 할 수 있다.

    buildTypes {
            release {
                minifyEnabled false
                manifestPlaceholders = [appLabel: "프로젝트명"]
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
                debuggable false
                signingConfig signingConfigs.config
                applicationVariants.all { variant ->
                    variant.outputs.all {
                        outputFileName = "${applicationName}-${variant.getFlavorName()}-${variant.buildType.name}-${defaultConfig.versionName}(${defaultConfig.versionCode}).apk"
                    }
                }
            }
            debug {
                applicationIdSuffix ".debug"
                minifyEnabled false
                ext.enableCrashlytics = false
                manifestPlaceholders = [appLabel: "(개발용)프로젝트명"]
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
                applicationVariants.all { variant ->
                    variant.outputs.all {
                        outputFileName = "${applicationName}-${variant.getFlavorName()}-${variant.buildType.name}-${defaultConfig.versionName}(${defaultConfig.versionCode}).apk"
                    }
                }
            }
        }

    productFlavors {
            korean {
            }
            mandarin {
            }
        }

(나의 경우는 개발용 앱과 릴리즈용 앱을 추출하고 나서 설치했을 때, 내가 잘못 추출 했을 때 쉽게 알아차리기 위해서 package명에 `suffix`를 추가했을 뿐만 아니라 `appLabel`도 다른 값을 주었다. 그래서 `release`와 `debug` 블럭이 나누어져있다.)

눈여겨 보아야 할 부분은 이 부분이다.

    variant.outputs.all {
                        outputFileName = "${applicationName}-${variant.getFlavorName()}-${variant.buildType.name}-${defaultConfig.versionName}(${defaultConfig.versionCode}).apk"
                    }

설정해줘야 하는 건 이게 끝이다. 다양한 문법이 인터넷에 올라와 있는데 이 문법이 가독성은 떨어지지만 잘 작동했다. 순서대로 `앱이름-앱flaver이름-앱buildType-버전이름-버전코드.apk` 이다. 내가 릴리즈용 앱을 한국어로 빌드한다면 apk파일명은 `앱이름-korean-release-1.0.0(1).apk` 로 뽑히게 된다.이 프로젝트는 해외 릴리즈용 프로젝트였기 때문에 언어 설정을 분리했었고, 그래서 flavor이름으로는 해당 언어를 나타냈다. 그리고 서버타입에 따라 buildType을 나타냈고, 버전 이름과 버전 코드를 그 뒤에 붙였다. <br><br>그렇지만 하루에 한 번만 내가 APK를 공유하는 건 아니기 때문에 이름을 수정해야 한다는 불편함은 여전히 있다. 그 불편함을 해결하는 아주 간단한 방법으로는 **버전코드를 Build 할 때마다 증가**시키거나 **타임 스탬프**를 찍는 것이다.

사실 모든 빌드 과정에서 버전 코드를 증가시키는 것은 효율적이지 않다. 나는 업데이트 횟수를 버전 코드를 통해 확인하는 편이라서 (프로젝트에서 세 번째 릴리즈가 나갔다면 버전 코드는 3이어야 함.) Timestamp를 이용하는 것이 좋다.
<br><br><br>
## apk 파일명에 추가로 Timestamp 찍기

---

그래들에서 타임스탬프를 찍는 방법은 간단하다.

    def getDate(){
        return new Date().format('yyyyMMdd-HHmmssSSS')
    }

gradle 마지막 줄에 위와 같은 메서드를 추가하고, 아까 apk 이름을 생성해 주는 소스에서 다음과 같이 추가한다.

    variant.outputs.all {
                        outputFileName = "${applicationName}-${variant.getFlavorName()}-${variant.buildType.name}-${defaultConfig.versionName}(${defaultConfig.versionCode})-${getDate()}.apk"
                    }

이렇게 똑같이 했을 때는 너무 구구절절하게 길어질 수 있으니 포맷을 적절하게 수정해서 사용하는 것을 추천한다. 
<br><br>
## 참고

---

- 빌드할 때마다 버전 코드를 올려주는 방법

[Autoincrement VersionCode with gradle extra properties](https://stackoverflow.com/questions/21405457/autoincrement-versioncode-with-gradle-extra-properties)
