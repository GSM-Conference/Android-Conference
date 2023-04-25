## Android CI
---
### CI/CD란??
![CI/CD](https://images.velog.io/images/cham/post/0f5c6eb0-9f80-4385-8e4d-5dbe70dd58e2/cicd.png)

Android에 CI를 어떻게 적용하는지 알기 전에 기본적인 개념인 CI/CD에 대해서 알아보고 가자!

### 1.CI

CI란, Continuous Integartion의 약자로 지속적인 통합을 의미한다.

지속적인 통합 개발을 진행하면서, 코드의 통합을 지속적으로 진행해 코드의 품질을 유지시키는 것이다. 따라서, Bitrise나 Jenkins와 같은 CI/CD 자동화 툴을 사용하지 않더라도, CI 자체는 가능하다.

하지만 CI는 지속적으로 진행되기에 매우 번거로운 일이므로 툴을 사용해 자동화 시키는 것이 일반적이다.

<br>

예시

자동화 X 
```md
1. 모든 개발자는 퇴근전에 코드를 push 한다.

2. push되어 통합된 코드가 정상 동작하는지 테스트한다.

3. 통합된 코드가 제대로 빌드 되는지 테스트한다.

4. 결과를 정리하여, 버그가 있다면 다음 날 업무 리스트에 추가한다.
```

<br>

자동화 O
```md
1. 모든 개발자는 퇴근 전에 코드를 push 한다.

2. 자동화 도구의 결과에 따라 버그가 있다면 다음 날 업무 리스트에 추가한다.
```

위 예시와 같이, 자동화 도구의 도입으로 지속적인 통합을 이루어내면서 테스트와 빌드의 과정이 자동화되어 보다 간편한 워크 플로우가 형성되었음을 알 수 있다.

<br>
<br>

### 2.CD

CD란 Continuous Deploy 또는 Continuous Delivery의 약자로 지속적인 배포를 의미한다.

소프트웨어가 항상 신뢰 가능한 수준에서 배포될 수 있도록 하는 것이다.

하지만 소프트웨어를 배포하기 위해서는 빌드와 테스트는 필수적이기 때문에, CD를 위해서는 CI가 선행되어야 한다.

### 요약 
CI : 빌드와 테스트의 자동화

CD : 배포의 자동화


<br>
<br>
<br>

## Android CI 적용!
---
CI/CD 를 도와주는 툴은 여러가지가 있는데 Jenkins, Circle CI, Github Actions 등이 있다. 이 툴들중 무료이면서 가장 쉽고 세팅이 적고 Github Repository 에서 바로 접근할 수 도 있어 Github Actions를 예시로 설명해보겠다.

<br>

### 시작하기
Github Actions에 들어가면 Android CI가 바로 뜨진 않는다. 'browse all category' 버튼을 눌러 더 찾거나 아니면 아래 사진처럼 검색을 하면 나온다.

![Github_Actions_Search](https://cdn.discordapp.com/attachments/1089420730963329074/1094934976094535812/image.png)

그러면 Repository의 ./github/workflows 폴더에 android.yml 폴더가 생긴다. 여기에 어떤 로직을 짜느냐에 따라 특정 브랜치에 Push or PR 을 했을 때 수행하는 WorkFlow를 만들 수 있다.

Github에서 원격 서버(Ubuntu)를 제공해주고, 그 공간에서 내가 올린 소스코드로 빌드 등을 해주는 원리이다.

(public repository에서는 무료이고, private 레포는 매월 2천 시간까지만 무료이고 이후부터는 유료이다.)

![Github_Repository_WorkFlow](https://cdn.discordapp.com/attachments/1089420730963329074/1094936808900538479/image.png)

구글링 해본 결과 저런식으로 놔두거나 push와 pull request 의 브랜치를 release로 바꾸는 경우라서 유추해보건데 작업이 열리는?? 실행되는?? 브랜치를 나타나는 것 같길래 난 master에서만 작업을 했기 때문에 그냥 그대로 뒀다

그렇게 작업을 하려고 했으나 오류가 떴다..
![Error]()
<br>


### 보안 파일 관리
원격에서 앱을 빌드하려면 한가지 문제가 생길 수 도 있다. 그것은 바로 자신이 **gitignore**로 보이지 않도록 명시한 보안파일들이디. 이것들이 없으면 아마 빌드 에러가 발생할 것인데, 이를 위해 Github에서 제공하는 Secret 기능도 사용하였다.
