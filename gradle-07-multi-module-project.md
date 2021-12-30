# Gadle (7)  Multi-module project 

먼저, IDE는 VSCode를 사용하여 설명한다. 


## project 생성 
프로젝트를 생성하는 방법은  [Gradle (01) 시작하기](gradle-01.md)을 참고한다. 

사실, gradle init로 프로젝트를 생성하면 multi module 프로젝트로 생성한다. settings.gradle 파일을 열어보면 app 모듈을 include하고 있다. 
```shell
rootProject.name = 'demo'
include('app')
```

빌드는 항상 root 프로젝트를 기준으로 진행할 예정이기 때문에 root 프로젝트 이외에는 

* gradle 폴더
* gradlew 등이 없다. 
* 오직, build.gradle와 src 폴더만 존재한다. 

core 폴더를 프로젝트 루트 폴더에서 생성하고 다음과 같이 구조를 만든다. 

```shell
📂project-root   # 생성된 프로젝트 루트 
  📂app          # 생성된 하위 모듈 , app 모듈은 core 모듈에 의존적이다.
     📂src
       📂main
         📂java   
    📄build.gradle
  📂core         # 생성한 하위 모듈 
     📄build.gradle
     📂src
       📂main
         📂java 
  📂gradle         
 📄gradlew
 📄settings.gradle  # 프로젝트 생성 시에 하위 모듈을 include하고 있다. 
```

## core 모듈 include
settings.gradle 파일을 열어서 다음과 같이 include에 core 모듈을 추가한다. 
```shell
rootProject.name = 'demo'
include 'app', 'core' 
```

## core 모듈 작업
### 패키지 생성
com.sogood.member 패키지 폴더를 생성하고 그 아래에 Member.java를 생성한다. 

```shell
📂project-root   # 생성된 프로젝트 루트 
  📂core         # 생성한 하위 모듈 
     📄build.gradle
     📂src
       📂main
         📂java 
           📂com
             📂sogood
               📂member
                📄Member.java
  📂gradle         
 📄gradlew
 📄settings.gradle  # 프로젝트 생성 시에 하위 모듈을 include하고 있다. 
```

### Member.java 작성

```java
package com.sogood.member;

public class Member {
  private String name; 
  private int age;
  public String getName() {
    return name;
  }
  public void setName(String name) {
    this.name = name;
  }
  public int getAge() {
    return age;
  }
  public void setAge(int age) {
    this.age = age;
  } 
}
```

### build.gradle 설정
다음과 같이 설정한다. 
```shell
apply plugin: 'java'        // 'java'라는 Gradle 플러그인 적용
sourceCompatibility = 1.8   // Java 호환 버전을 1.8로 설정
repositories {
    mavenCentral()
}
dependencies {
}
```

## app 모듈 작업 
### 패키지 생성
com.sogood.member 패키지를 생성하고 MemberService.java를 생성한다.

```shell
📂project-root   # 생성된 프로젝트 루트 
  📂appe         # 생성한 하위 모듈 
     📄build.gradle
     📂src
       📂main
         📂java 
           📂com
             📂sogood
               📂member
                📄MemberService.java
  📂gradle         
 📄gradlew
 📄settings.gradle  # 프로젝트 생성 시에 하위 모듈을 include하고 있다. 
```

**MemberService.java**

```java
package com.sogood.member;

public class MemberService {
}

```

### build.gradle 설정
이제 core 모듈에서 생성한 Member.java를 참조해야 하는데 의존성을 먼저 설정해야 한다. app 모듈의 build.gradle을 열고 project(':core')를 사용하여 의존성을 추가한다. 

```shell
dependencies {
    // Use JUnit Jupiter for testing.
    testImplementation 'org.junit.jupiter:junit-jupiter:5.7.2'

    // This dependency is used by the application.
    implementation 'com.google.guava:guava:30.1.1-jre'
    implementation  project(':core')  // 의존성 추가 
}
```

### MemberService.java 작성

그런다음 MemberService.java르 ㄹ 다음과 같이 작성한다. 오류가 발생하지 않는 것을 알 수 있다. 
```java
package com.sogood.member;

public class MemberService {
  public Member getMember() {
    Member member = new Member();
    member.setName("Hong");
    member.setAge(10);
    return member; 
  }
}
```


## 빌드하기 
빌드하면 정상적으로 컴파일 되는 것을 알 수 있다. 
```shell
./gradlew build
```
## 지금까지 요약 
이러한 프로젝트 구조는  최상위 프로젝트에서 core 모듈과 app 모듈을 하위 프로젝트로 관리하겠다는 의미이다. 

## 퍼블리싱 하기 
Maven의 Local repository를 사용하도록 할 것인데 빌드한 라이브러리를 Maven Local repository에 저장해보자. 

```shell
plugins {
    id 'java'
    id 'maven-publish' // maven-publish 플러그인 추가
}

repositories {
    mavenCentral()
    mavenLocal() // (.m2/repository)에 있는 경우 참조
}

publishing {
    publications {
        // 해당 정보를 입력하여 타 프로젝트에서 해당 라이브러리를 참조한다.
        maven(MavenPublication) {
            groupId = 'com.sogood' // groupId 추가
            artifactId = 'core' // artifactId 추가
            version = '1.0' // 버전 정보 추가

            from components.java
        }
    }
}
```

추가로 mavenLocal()을 사용할 수 있는데 이는 메이븐 로컬 (Local) 레파지토리 (.m2/repository)에 있는 경우 참조할 수 있도록 한다.



다음 명령어를 실행하여 Maven Local repository에 jar 형태로 저장한다.
```shell
$ gradle publishToMavenLocal
```

> **주의:** 
> 아무리 MAVEN_HOME , MAVEN_USER_HOME 환경 변수를 설정해봐도 gradle에서는 jar 파일을  c:/사용자/{user_name}/.m2/repository에 다운로드 받는다. 위의 publishToMavenLocal로 퍼블리싱해도 사용자 홈 디렉토리의 .m2/repository에 퍼블리싱한다. 
> 해결방법은 **settings.xml 파일을 .m2 디렉토리에 두는 것**이다. 



아래 URL에서 자세한 내용을 참조한다. 

https://docs.gradle.org/current/userguide/publishing_maven.html




## Multi-module 프로젝트에서 의존성 관리 
프로젝트를 구성할 때 Local에 정의된 모듈의 의존을 정의하는 방법이다. Local의 모듈 혹은 프로젝트를 끌어와서 사용하는 경우에는 settings.gradle 파일에 설정 된 "include:"로 정의한 프로젝트의 이름과 같아야 한다. 

**settings.gralde**
```shell
include 'app', 'core' 
```


**app/build.gradle**
```shell
dependencies {
    implementation  project(':core')
}
```

## 다른 프로젝트 소스 참조
예룰들어 common library를 개발하는 개발자는 common library의 repository에는 접근할 수 있지만 다른 개발자들은 접근할 수 없다. 보통 다른 개발자들은 dependencies에 jar 파일을 포함하여 개발을 한다. 그러가 common library를 개발하는 개발자는 디버깅이 필요할 수도 있다. 이런 경우 다른 프로젝트의 소스를 참고할 수 있도록 설정이 필요하다. 


IntelliJ에서는 두 개의 프로젝트를 모듈로 추가한다. VSCode에서는 같은 폴더 아래 두거나 workspace로 묶는다. 같은 폴더에 두는 것이 좋다. 

다음과 같은 구조일 것이다. 

```shell
📂common-lib-project 
   📄settings.gradle  
📂web-project 
  📂web
    📄build.gradle      
  📄settings.gradle  
```
각각이 settings.gradle 파일이 있으므로 디폴트로 소스를 참고할 수는 없다. 

이럴 때는 web-project에서 settings에 다음과 같이 설정한다. 
```shell
// settings.gradle
project(':common').projectDir =new File(settingsDir, '../common-lib-project')
```

web-project의 build.gradle에서는 다음과 같이 jar 의존성을 추가한다. 
```shell
// web-project/web/build.gradle
dependencies {
    implementation 'com.sogood:my-core:1.0.0'
}
```


그런데 이런 식으로 설정하려면 jar 파일은 maven local repository에 배포되어야 한다. 






