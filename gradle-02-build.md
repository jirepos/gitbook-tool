# Gradle (2) - Java 또는 JVM 프로젝트 Build하기 

> 이 문서의 내용는 java 또는 JVM 프로젝트를 빌드하는 것에 배경지식을 얻는데 활용한다. 더 자세한 내용은 Rererences를 참고한다. 



Gradle은 Apache Maven에서 몇 가지 규칙을 차용하는 JVM 기반 프로젝트를 구축하기 위해 구성보다 규칙적인 접근 방식을 사용한다.  특히 **소스 파일 및 리소스에 대해 동일한 기본 디렉토리 구조를 사용하며 Maven 호환 리포지토리와 함께 작동**한다.


## 소개 
Java 프로젝트에 대한 가장 간단한 빌드 스크립트는 Java 라이브러리 플러그인을 적용 하고 선택적으로 프로젝트 버전을 설정하고 사용할 Java 도구 체인 을 선택한다.

**Java library plugin 적용**
```shell
plugins {
    id 'java-library'
}

java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(11)
    }
}

version = '1.2.1'
```

Java 라이브러리 플러그인을 적용하면 다음과 같은 다양한 기능을 얻을 수 있다. 

* src/main/javacompile Java 아래의 모든 Java 소스 파일을 컴파일 하는 작업
* compileTestJava에서 소스 파일에 대한 작업 src/test/java 에서 테스트를 실행 하는 작업
* src/main/resources  에서 컴파일된 클래스와 리소스를 \<project\>-\<version\>.jar 이라는 단일 JAR로 jar패키징 하는 작업
* Javadoc을 생성 하는 작업




## 소스 세트(source sets)를 통해 소스 파일 선언 
Gradle에서 Source sets를 처음 도입했다.  소스 파일과 리소스가 코드, 단위 테스트 및 통합 테스트와 같은 유형별로 논리적으로 그룹화된다. 각 논리 그룹을 소스 세트를 사용하면 같은 디텔토리에 있을 필요가 없다. 

소스 세트는 컴파일의 여러 측면을 함께 묶는 강력한 개념. 
* 소스 파일 및 위치 
* 필요한 종속성을 포함한 클래스 경로 
* 컴파일된 클래스 파일 위치 


## 종속성 관리 

Java 프로젝트에 대한 종속성을 지정하려면 다음 세 가지 정보만 있으면 된다. 

* 이름 및 버전과 같이 필요한 종속성
* 필요한 것(예: 컴파일 또는 실행)
* 찾을 곳

처음 두 개는 dependencies {} 블록에 지정되고 세 번째 는 repositories {}블록에 지정된다. 

**build.gradle** 
```shell
repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.hibernate:hibernate-core:3.6.7.Final'
}
```

세 가지 요소에 대한 Gradle 용어는 다음과 같다. 

* 리포지토리 (예: mavenCentral()) — 종속성으로 선언한 모듈을 찾을 위치
* 구성 (예: implementation) — 모듈 컴파일 또는 실행과 같은 특정 목표를 위해 함께 그룹화되는 명명된 종속성 모음 — 보다 유연한 Maven 범위 형식
* 모듈 좌표 (예: org.hibernate:hibernate-core-3.6.7.Final) — 종속성의 ID, 일반적으로 ' \<group\> : \<module\> : \<version\> '(또는 Maven 용어로 ' \<groupId\> : \<artifactId\> : \<version\> ' 형식)

구성에 관한 한 주요 관심 사항은 다음과 같다.  



* **compileOnly** — 프로덕션 코드를 컴파일하는 데 필요하지만 런타임 클래스 경로의 일부가 아니어야 하는 종속성의 경우
* **implementation**(대체 compile) — 컴파일 및 런타임에 사용
* **runtimeOnly**(supersedes runtime) — 컴파일이 아닌 런타임에만 사용됨
* **testCompileOnly**- compileOnly테스트를 위한 것 외에는 동일
* **testImplementation** — 동등한 테스트 implementation
* **testRuntimeOnly** — 동등한 테스트 runtimeOnly


## 코드 컴파일 

다음 규칙을 따르면 프로덕션 코드와 테스트 코드를 모두 컴파일하는 것이 매우 쉬울 수 있다. 


* 프로덕션 소스 코드를 src/main/java 디렉토리에 넣는다. 
* src/test/java 아래에 테스트 소스 코드를 넣어라. 
* compileOnly또는 implementation구성 에서 프로덕션 컴파일 종속성을 선언한다.
* testCompileOnly또는 testImplementation구성 에서 테스트 컴파일 종속성을 선언한다. 
* compileJava프로덕션 코드 및 compileTestJava테스트에 대한 작업 실행


**파일 및 디렉토리 위치 사용자 정의**
프로덕션 코드에 src 디렉토리를 사용 하고 테스트 코드에 테스트 하는 레거시 프로젝트가 있다고 상상해 보라. 기존의 디렉토리 구조는 작동하지 않으므로 Gradle에 소스 파일을 찾을 위치를 알려야 한다. 소스 세트 구성을 통해 이를 수행한다. 

각 소스 세트는 클래스 파일의 리소스 및 출력 디렉토리와 함께 소스 코드가 있는 위치를 정의한다. 다음 구문을 사용하여 규칙 값을 재정의할 수 있다. 

예 3. 사용자 정의 소스 디렉토리 선언
```shell
sourceSets {
    main {
         java {
            srcDirs = ['src']
         }
    }

    test {
        java {
            srcDirs = ['test']
        }
    }
}
```
이제 Gradle은 각각의 소스 코드를 위해 src와 test 직접적으로 찾기만 할 것이다. 관례를 오버라이드하려면 어떻게 하지?  간단히 추가 소스 디렉토리를 추가하고 싶다면, 분리하기 원하는 써드파티 소스 코드을 포함하는 하나가 될 것이다. 
```shell
sourceSets {
    main {
        java {
            srcDir 'thirdParty/src/main/java'
        }
    }
}
```

## 컴파일러 옵션 변경 

대부분의 컴파일러 옵션은 compileJava및  compileTestJava와 같은 해당 작업을 통해 액세스할 수 있다 compileTestJava. 이러한 작업은 JavaCompile 유형 이므로 포괄적인 최신 옵션 목록을 보려면 작업 참조를 읽어라. 

```shell
compileJava {
    options.incremental = true
    options.fork = true
    options.failOnError = false
}
```

**특정 Java version에 targeting**
기본적으로 Gradle은 Java 코드를 Gradle을 실행하는 JVM의 언어 수준으로 컴파일한다. Java 도구 체인을 사용 하면 빌드에 정의된 지정된 Java 버전이 컴파일, 실행 및 문서화에 사용되는지 확인하여 해당 링크를 끊을 수 있다. 그러나 작업 수준에서 일부 컴파일러 및 실행 옵션을 재정의할 수 있다. 

버전 9부터는 코드가 최신 버전의 API를 사용하지 않는지 확인하면서 이전 Java 버전에 대한 바이트코드를 생성하도록 Java 컴파일러를 구성할 수 있다. Gradle은 이제 Java 컴파일을 위해 이 릴리스 플래그를 CompileOptions직접 지원한다.

> Java 10에서 수정된 Java 9 의 버그 로 인해 Gradle은 releaseJava 9로 컴파일할 때 플래그를 활용할 수 없습니다

**Java 릴리스 플래그 설정**
```shell
compileJava {
    options.release = 7
}
```

Java 컴파일러에 대한 기록 옵션은 계속 사용할 수 있다. 

**sourceCompatibility**

소스 파일을 처리해야 하는 Java 언어 버전을 정의합니다.

**targetCompatibility**

코드가 실행되어야 하는 최소 JVM 버전을 정의합니다. 즉, 컴파일러가 생성하는 바이트 코드의 버전을 결정합니다.


이러한 옵션은 JavaCompile 작업 별로 또는 java { }이름이 같은 속성을 사용하여 모든 컴파일 작업 의 확장 에서 설정할 수 있다. 


## 패키징 및 퍼블리싱 
이 섹션에서는 Java 라이브러리 플러그인에서 제공하는 베어본에 중점을 둘 것이다. 

기본적으로 Java 라이브러리 플러그인은 jar컴파일된 모든 프로덕션 클래스와 리소스를 단일 JAR로 패키징 하는 작업을 제공한다.  이 JAR도 assemble태스크에 의해 자동으로 빌드된다. 또한, 플러그가 제공하도록 구성 될 수 javadocJar및 sourcesJar원한다면 패키지 자바 독 소스 코드 작업, 게시 플러그인을 사용하는 경우 이러한 작업은 게시하는 동안 자동으로 실행되거나 직접 호출될 수 있다.

**Javadoc 및 소스를 게시하도록 프로젝트 구성**

```shell
java {
    withJavadocJar()
    withSourcesJar()
}
```

**JAR 매니페스트 수정**
Jar, War및 Ear작업 의 각 인스턴스 에는 해당 아카이브로 이동 manifest하는 MANIFEST.MF 파일 을 사용자 정의할 수 있는 속성이 있다 . 다음 예제는 JAR 매니페스트에서 속성을 설정하는 방법을 보여준다.


**MANIFEST.MF의 사용자 정의**
```shell
jar {
    manifest {
        attributes("Implementation-Title": "Gradle",
                   "Implementation-Version": archiveVersion)
    }
}
```

## 빌드 청소
Java 라이브러리 플러그인 clean은 기본 플러그인 을 적용하여 프로젝트에 작업을 추가한다.  이 작업은 단순히 \$buildDir디렉터리의 모든 것을 삭제 하므로 빌드에서 생성된 파일을 항상 디렉터리에 넣어야 하는 이유다. 작업은 삭제 의 인스턴스이며 해당 dir속성 을 설정하여 삭제할 디렉터리를 변경할 수 있다. 


## References
[Building Jav & JVM projects](https://docs.gradle.org/current/userguide/building_java_projects.html#sec:building_java_applications)

