---
title: "quasar + cordova emulater 연동 및 배포"
keywords: vue, quasar, cordova, android, emulater, 연동, apk배포
tags: [Frontend]
sidebar: mydoc_sidebar
summary: quasar 로 cordova 에서 개발환경과 배포 apk 추출에 대한 기록.
---

cordova가 개념적으론 간단한데, 설정이 복잡하다.
window, mac, linux 에 따라 조금씩 차이가 있고, android 냐 mac 이냐 에 따라 설정이 다른 부분이 
있다. 설정들은 항상 개인 환경에 따라 다른 오류를 내뱉는 경우가 많아 힘들구낭.
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
{% include image.html file="newBlog/images/quasar1_1.png" alt="path" %}

gradle path 도 설정해주자
{% include image.html file="newBlog/images/quasar1_2.png" alt="path" %}

그리고 나머진 홈페이지 설명대로 sdk 매니저에서 필요한 android OS 버전들을 선택한다.
선택할 때 Android 9.0-pie 버전을 꼭 선택하라고 안내하고 있다.

##
여기서 약간의 헤맴이 있었는데.
기존 로컬 환경 세팅이 java가 julu11로 돌아가고 있었다. 
이클립스와 여러 IDE 들의 path 가 모두 julu 를 바라보고 있어서
특히나 중요한 java 쪽 소스들이 모두 에러를 내뱉고 있었다. (크흑 예상은 됬는데 생각보다. 많더라 stream 가튼거 그런거)

일단은 침착하게 


