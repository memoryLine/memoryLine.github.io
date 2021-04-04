---
title: "quasar + cordova emulater 연동 및 배포"
keywords: vue, quasar, cordova, android, emulater, 연동, apk배포
tags: [Frontend]
sidebar: mydoc_sidebar
permalink: front_quasar1.html
summary: quasar 로 cordova 에서 개발환경과 배포 apk 추출에 대한 기록.
---

cordova가 개념적으론 간단한데, 설정이 복잡하다.

window, mac, linux 에 따라 조금씩 차이가 있고, android 냐 mac 이냐 에 따라 설정이 다른 부분이 
있다. 

설정들은 항상 개인 환경에 따라 다른 오류를 내뱉는 경우가 많아 힘들구낭.

게다가 안드로이드 스튜디오까지..ㅠ

quasar는 웹앱을 수월하게 하기 위해 cordova 와 ionic 을 지원해 주는데,

그중에서 cordova 를 안드로이드와 연동하는 부분을 확인 해본다.

## 테스트 환경
window10

java open-jdk 1.8.0

java julu 11.37

안드로이드 스튜디오

gradle 4.10

node 14 (노드 버전이 10보단 크고 14보다 크지말자. quasar 권장.)

vue 

## 먼저 quasar 홈페이지 안내를 살펴보자.
[quasar cordova 설정안내](https://quasar.dev/quasar-cli/developing-cordova-apps/preparation)

읽어 보면 그대로 따라하면 되는데, 몇 가지 잘 안됬던 부분을 짚어보겠다.

## 1.cordova install
```
$ npm install -g cordova
```

## 2.안드로이드 스튜디오 설치

설치 파일을 다운 받고 다음 다음 으로 설치한다. (20~30분은 걸렸던것 같다.)

설치 하고 나면 우린 윈도우 환경이라서 cordova에서 알 수 있게 환경변수 셋팅을 해준다.

ANDROID_HOME 은 이제 deprecated 되어서 사용 안되며, ANDROID_SDK_ROOT 로 사용하라는 코멘트를 볼수 있다.

아래와 같이 PATH 편집을 해주고,

{% include image.html file="front/quasar1_1.png" alt="path" %}

gradle path 도 설정해주자

{% include image.html file="front/quasar1_2.png" alt="path" %}

그리고 나머진 홈페이지 설명대로 sdk 매니저에서 필요한 android OS 버전들을 선택한다.

선택할 때 Android 9.0-pie 버전을 꼭 선택하라고 안내하고 있다.

## 3. quasar 에 cordova 추가

이제 현재 우리가 작업하는 quasar 소스상에 cordova 소스가 들어 올수 있게 작업한다.

cordova mode 설치

```
$ quasar mode add cordova
```
작업하던 quasar 프로젝트로 가보면 src-cordova 폴더가 생겨있는걸 볼수 있다.

들어가보자

```
$ cd src-cordova
```


## 4. cordova 가 정상인지 확인하는 과정
```
$ cordova platform add android
```

해당 명령어 진행 후 잘 설치 된건지 확인하는 마지막 과정을 보자.

```
$ cordova requirements
```

짠~ 아래와 같은 화면이 나오면 정상이지만,

{% include image.html file="front/quasar1_3.png" alt="path" %}

한번에 되지 않았다. 

Java JDK 항목에 java를 찾을 수 없다고 나왔다.

cordova spec확인을 하다보니. cordova 가 굴러가기 위한 jdk 버전을 8버전으로 고정한듯 하다는 글을 보았다.

openjdk8을 찾아 설치한뒤 Java JDK 항목이 해결. 되.었.다.

여기서 추가 삽질이

현재 프로젝트 진행상 쓰고 있는 java는 julu였고, 일단은 openjdk 8로 변경 작업을 하였다.

기존 java를 지우지 않고 java version 이 변경이 되질 않았는데, 여러시도 끝에

 - openjdk 8 설치
 - 기존 julu 삭제
 - openjdk 8 로 버전 변경됨.
 - julu 재설치 (이클립스 등 몇가지 IDE에서 사용하고 있어서) 

로 해결하였다.

## 5. 개발 환경 마무리 폰에서 할거냐, 안드로이드 스튜디오에서 할거냐.

quasar 는 두가지 방법 모두 지원하는데.

1.  폰에서 하고 싶을 경우

    * USB 디버깅을 해제하고, 

    * adb 설치(연결이 안된다면?)

    아래 명령어로 실행 가능하다.

    ```
    $ quasar dev -m cordova -T android
    ```

    에뮬레이터의 프로파일이 없으면 안되는 경우도 있는데 그럴땐 안드로이드 스튜디오에서 프로필을 만들어준다.

    그 때는 아래와 같이 실행한다.

    ```
    $ quasar dev -m cordova -T android -e [emulate_name]
    ```
2.  안드로이드 스튜디오에서 할 경우

    아래와 같이 실행하면 스튜디오가 자동으로 뜨고, 에뮬레이터에 작업 결과가 나타난다.

    (오래걸려서 쓰기 싫더라.)

    ```
    $ quasar dev -m android --ide
    ```

## 6. 배포 가능한 apk 를 뽑아보자. 

이제 개발 환경까진 만들어 보았고, 마지막으로 배포를 위한 apk를 추출해보자.

```
$ quasar build -m android
```

홈페이지에는 위 명령어로 에뮬레이터에 설치를 진행한다. 

하지만 그게 잘 안되었다. 왜인지 궁금하지 않아. 나는 apk 만 있으면 되니까.

apk 를 만들기 전에 알아야 할게 안드로이드에 apk 설치할 때 apk의 출처를 증명 할 무언가
가 등록되어야 그 apk 를 등록 가능하다.

그래서 다음 작업은 apk 에 릴리즈 단계를 부여하고, 우리가 만든 key 를 apk 심는 과정을 진행하겠다.

1.  플러그인 제거
    ```
    $ cordova plugin rm org.apache.cordova.console --save
    ```

2.  AndroidManifest.xml 항목 정리
    AndroidManifest.xml 파일에 android : debuggable 를 삭제한다. 

    debuggable = true 를 빌드도구에서 자동으로 삽입하지만,

    릴리즈 빌드시 자동으로 false로 바꿔주기 때문에 특정값을 지정하여 실수 할 수 있는 부분을 없앤다.

3.  cordova 에 릴리즈 빌드가 가능하도록 설정

    ```
    $cordova build --release android
    ```

    이 작업이 끝나면 아래 처럼 release 폴더 안에 app-release-unsigned.apk 가 생겨있다.

    {% include image.html file="front/quasar1_4.png" alt="path" %}

4.  이제 심을 키를 생성하여 저장하자.

    ```
    $keytool -genkey -v -keystore [원하는네임]-mobileapps.keystore -alias [원하는네임]mobileapps -keyalg RSA -keysize 2048 -validity 10000
    ```

    안된다면 apk 가 있는 폴더까지 이동하자.

    몇 가지 질문을 하는데 비밀번호 외에는 대충 작성해도 된다.

    이동한 폴더에 [원하는네임]-mobileapps.keystore 란 이름으로 생성되었을것이다.

5.  jarsigner 를 이용하여 apk 에 sign 한다.

    ```
    $jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore [원하는네임]-mobileapps.keystore app-release-unsigned.apk [원하는네임]mobileapps
    ```

    아마 이 작업이 한번에 되질 않을건데

    명령어는 apk 가 있는 곳에서 하는것이 아닌, 아까 저장한 키스토어에서 해야한다.

    위의 구조 사진에서 output/ 밑에 apk 가 있다면, apk 를 키스토어 있는곳으로 이동시킨 후 명령어를 재실행 해보자.

    성공하면, 위의 사진처럼 [원하는네임].apk 가 생성될것이다.

    해당 apk를 복사하여, 실제 폰에 설치해보자. 몇 가지 설정할게 더 있어. play store 에서 경고 문구를 띄우지만

    설치는 잘 되고 웹으로 코딩했던 모든 내용이 잘 동작한다. 







