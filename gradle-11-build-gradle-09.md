# Gadle (11) - build.gradle #9 


## 공통 설정 적용

프로젝트 간 공통 설정이 필요할 수 있다. 이러한 부분은 최상위 프로젝트의 build.gradle 파일에 모든 하위 프로젝트에 적용되는 공통 설정을 한다.

```shell
allprojects {
    apply plugin: 'java'

    group 'org.example'
    version '1.0-SNAPSHOT'

    repositories {
        mavenCentral()
    }

    dependencies {
        testImplementation 'org.junit.jupiter:junit-jupiter-api:5.6.0'
        testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine'
        implementation 'com.microsoft.azure:msal4j:1.8.0'
        implementation 'com.microsoft.graph:microsoft-graph:2.4.1'
        implementation 'org.slf4j:slf4j-nop:1.8.0-beta4'
    }
}
```


* allprojects 최상위 프로젝트를 포함한 모든 하위 프로젝트에 공통으로 적용한다면 이것을 사용한다. 
* subprojects 내의 모든 설정값들은 모든 하위 프로젝트에 적용된다. 


프로젝트 간 공통 설정이 필요할 수 있다. 이러한 부분은 최상위 프로젝트의 build.gradle 파일에 모든 하위 프로젝트에 적용되는 공통 설정을 한다


```shell
allprojects {
    apply plugin: 'java'

    group 'org.example'
    version '1.0-SNAPSHOT'

    repositories {
        mavenCentral()
    }

    dependencies {
        testImplementation 'org.junit.jupiter:junit-jupiter-api:5.6.0'
        testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine'
        implementation 'com.microsoft.azure:msal4j:1.8.0'
        implementation 'com.microsoft.graph:microsoft-graph:2.4.1'
        implementation 'org.slf4j:slf4j-nop:1.8.0-beta4'
    }
}
```

프로젝트 별로 상세한 설정이 필요한 경우 아래와 같이 각 프로젝트 별로 설정을 따로 나눌 수 있다.

 
```shell
def coreProject = [project(':core')]
def libProject  = [project(':mylib’)]

allprojects {
    apply plugin: 'java'
    group 'org.example'
    version '1.0-SNAPSHOT'
    repositories {        mavenCentral()    }
}
configure(coreProject) {
    dependencies {
        testImplementation 'org.junit.jupiter:junit-jupiter-api:5.6.0'
        testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine'
        implementation 'com.microsoft.azure:msal4j:1.8.0'
        implementation 'com.microsoft.graph:microsoft-graph:2.4.1'
        implementation 'org.slf4j:slf4j-nop:1.8.0-beta4'
    }
}
configure(libProject) {
    dependencies {
        testImplementation 'org.junit.jupiter:junit-jupiter-api:5.6.0'
        testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine'
        implementation 'com.microsoft.azure:msal4j:1.8.0'
        implementation 'com.microsoft.graph:microsoft-graph:2.4.1'
        implementation 'org.slf4j:slf4j-nop:1.8.0-beta4'
    }
} 

```

## buildscript 블록 
gradle.build 파일에 다음과 같이 buildScript 블럭을 만들 수 있다. 
```shell
buildscript { 
   // ... 생략 
}
```
이것은 빌드 스크립트 자체를 빌드하는 과정에서 필요한 외부 라이브러리를 클래스패스에 추가하는 기능을 한다. 중괄호로 묶은 내용(closure)을 ScriptHandler로 전달한다. ScriptHandler는 클로저(설명 TODO)를 인자로 받는 두 개의 메소드(repositories와 dependencies)를 갖고 있다. 빌드 스크립트에서 클래스패스를 추가할 경우에는 구성(configuration) 이름으로  classpath 문자열이 필요하다.

**빌드 스크립트 의존성 추가**

buildScript는 Gradle 빌드 스크립트 자체를 위한 의존성이나 변수,Task, Plugin 등을 지정할 수 있다. 서드파티 플러그인이나 Task, Class 등을 빌드 스크립트 내에서 추가로 사용하려면 해당 의존성을 추가해야 한다. build.gradle 자체를 실행하기 위한 것이라고 보면 된다.

* 소스코드를 빌드하고 실행하는데 필요한 글로벌 레벨의 depencies & repositories 설정
* build.gradle 파일 그 자체를 위한 것. Package Manager / Docker file / spirng-boot-gradle-plugin과 같이 빌드에 필요한 의존성 추가
* 따라서 소스코드 컴파일과 같은 빌드 작업을 시작하기 전에, 빌드 시스템 준비 단계에서 제일 먼저 실행되는 블록
* 결국 빌드 자체를 위한 의존성 & 레포를 설정하는 곳


Spring 부트 기반 프로젝트를 진행하는 경우, 이를 위한 spring-boot-gradle-plugin 의존성을 추가해야 한다. Spring Boot 사용 시 executable jar나 war 패키징, 앱 실행, spring-boot-dependencies의 dependency management 기능 등을 사용할 수 있게 하는 플러그인이다.


```shell
buildscript {
    ext {
       springRepo = 'http://repo.spring.io/libs-release'
    }

    repositories {
       maven { url springRepo }
    }

    dependencies {
       classpath "org.springframework.boot:spring-boot-gradle-plugin:2.0.4.RELEASE"
    }
}
```


* 소스코드를 빌드하고 실행하는데 필요한 글로벌 레벨의 depencies & repositories 설정
* build.gradle 파일 그 자체를 위한 것. Package Manager / Docker file / spirng-boot-gradle-plugin과 같이 빌드에 필요한 의존성 추가
* 따라서 소스코드 컴파일과 같은 빌드 작업을 시작하기 전에, 빌드 시스템 준비 단계에서 제일 먼저 실행되는 블록
* 결국 빌드 자체를 위한 의존성 & 레포를 설정하는 곳



