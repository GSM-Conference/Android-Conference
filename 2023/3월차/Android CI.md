## Android CI
---
### CI/CD란??
![CI/CD](https://images.velog.io/images/cham/post/0f5c6eb0-9f80-4385-8e4d-5dbe70dd58e2/cicd.png)

Andorid에 CI를 어떻게 적용하는지 알기 전에 기본적인 개념인 CI/CD에 대해서 알아보고 가자!

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
여러가지 적용 방법이 있지만 그중에 Github를 통해 구성해보겠다.

```md
1. Github 생성
2. CI구성 : Action 설정 및 옵션 설정
```

### 1. Github Repository 생성
Repository 를 생성한 후 브랜치를 main - release  develop 으로 구성해 준다. release 브랜치에서 merge 되면 CI/CD가 배포되도록 해본다.

![Repository](https://velog.velcdn.com/images/wonsh2000/post/da7dd136-6d15-463b-93d4-812d5f12fc09/image.png)

<br>

### 2. CI 구성 (Github Action 설정)
상단 탭에 Actions 라는 버튼을 클릭하면 프로젝트별 환경을 제공하고 있다.

workflows/파일명.yml 인 새로운 파일이 생성된다. Android CI를 이용하여 default 환경을 이용하여 빌드를 돌려본다. 물론 실제 환경에서는 Dev, QA, Stage, Prd 환경별로 구성하고 Signing 과 난독화 등 많은 작업들이 추가된다.

```yml
name: Android CI

on:
  push:
    branches: [ release ]
  pull_request:
    branches: [ release ]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: set up JDK 11
      uses: actions/setup-java@v3
      with:
        java-version: '11'
        distribution: 'temurin'
        cache: gradle

    - name: Grant execute permission for gradlew
      run: chmod +x gradlew
    - name: Build with Gradle
      run: ./gradlew build
```

아래와 같이 빌드가 정상적으로 돌아간 것을 확인 할 수 있다.

![Check](https://velog.velcdn.com/images/wonsh2000/post/5c2ebc8c-ee84-462b-8978-e225eacb36c2/image.png)

<br>
<br>

### 옵션. Ktlint와 Detekt 추가
코드의 품질을 높이기 위해 Ktlint와 Detekt도 아래와 같이 추가해준다. 린트와 정적 분석기 이다. 이를 하기 위해서는 프로젝트에 아래와 같이 추가한다,

<br>

> 공식 페이지 : https://ktlint.github.io/

```gradle
plugins {
    id("org.jlleitschuh.gradle.ktlint") version Versions.BuildUtil.KtLint
}

allprojects {
    ...
    apply {
        plugin("org.jlleitschuh.gradle.ktlint")
    }
}
```

프로젝트에 위처럼 추가헀다면 다시 yml 파일로 돌아와서 아래처럼 추가한다.

```yml
- name: Run ktlint
  run: ./gradlew ktlintCheck

- name: Run detekt
  run: ./gradlew detekt
```


