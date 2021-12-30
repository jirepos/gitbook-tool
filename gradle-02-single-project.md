# Gradle (02) - 단위 프로젝트 구조 이해

이 문서에서는 단위 프로젝트의 구조를 이해하고 build.gradle을 가지고 작업하는 방법에 대해서 이해한다. 


## 프로젝트 구조 

gradle init를 사용하여 single 이라는 프로젝트를 생성하면 구조는 다음과 같다. 

```shell
📂single   # 생성된 프로젝트 루트 
  📂lib          # 생성된 하위 모듈 , app 모듈은 core 모듈에 의존적이다.
     📂src
       📂main
         📂java   
    📄build.gradle
  📂gradle         
 📄gradlew
 📄settings.gradle  # 프로젝트 생성 시에 하위 모듈을 include하고 있다. 
```

위에 폴더 구조를 보면 gradle init는 기본적으로 multi modoule 프로젝트를 할 수 있는 구조로 폴더들을 만든다. 

settings.gradle 파일이 있는 곳이 프로젝트 루트이고 루트 프로젝트이다. 그래들 작업을 실행할 때는 gradlew가 루트에 있기 때문에 프로젝트 루트에서 작업해야 한다. 


프로젝트 생성시에 project type을 liorary로 생성했기 대문에 lib 이라는 하위 프로젝트가 생셩되었다. single은 루트 프로젝트가 된다. 

> VSCode에서 프로젝트를 열 때는 single 폴더를 선택한다. 


Settings 인스턴스와 settings.gradle설정 파일 사이에는 일대일 대응하는데 프로젝튿들 간의 관계를 설정한다고 보면 된다. 

* 루트 프로젝트의 프로젝트 디렉토리는 기본적으로 설정 파일이 포함된 디렉토리이다.
* 루트 프로젝트의 이름은 기본적으로 설정 파일을 포함하는 디렉토리의 이름이다.

그럼 settings.gradle 파일을 보자.  rootProject.name이 폴더 이름과 같고 하위의 lib 프로젝트를 include 하고 있다. 

```shell
rootProject.name = 'single'
include('lib')
```


정리하자면 다음과 같은 구조이다. 

```shell
📂single   # 루트 프로젝트 
  📂lib          # 하위 프로젝트
    📄build.gradle
  📂gradle         
 📄settings.gradle  
```



> Gradle 프로젝트를 VSCode에서 작업할 때는 Java 환경 설정 이외에는 별달리 할 것은 없다. 이 문서에서는 설명하지 않는다. 다른 문서를 참고해라. 

생성된 하위 프로젝트는 소스 구조가 Maven과 동일하다 별도로 설정한 것은 없다. 



프로젝트를 생성할 때 패키지 이름을 com.sogood으로 설정했다면 src/main/java 아래의 com.sogood 패키지에 Library.java가 생성되어 있다. 

```java
// Library.java 
package com.single;

public class Library {
    public boolean someLibraryMethod() {
        return true;
    }
}
```

이제 빌드를 해보자. 
## 빌드하기 
Terminal을 열고 루트 프로젝트에서 다음을 입력한다. 
```shell
cd single // root로 이동 
./gradlew build
```

빌드하면 프로젝트 이름의 jar 파일이 build/libs에 생성이 된다. 

```shell
📂single   # 루트 프로젝트 
  📂lib          # 하위 프로젝트
    📂build      
      📂libs     
        📄lib.jar
    📄build.gradle
  📂gradle         
 📄settings.gradle  
```

lib 프로젝트는 library 프로젝트이므로 plugins에 java-library 플러그인을 추가했다. 
```shell
// build.gradle 
plugins {
    // Apply the java-library plugin for API and implementation separation.
    id 'java-library'
}
```

Gradle 빌드 툴은 기본적인 라이프 사이클을 제공하지 않는다. Maven 빌드 툴은 빌드 설정 파일이 상속개념이지만 그래들은 상속보다는 구성 Composition을 통한 확장을 선택했다. 이 구성을 통한 확장의 핵심에 plugin이 있다. “apply plugin : ‘java’”와 같이 Java 플러그인을 사용하도록 설정하는 순간 자바 프로젝트에 대한 라이프 사이클이 추가된다.

gradle은 환경을 구축할 때 매번 같은 작업을 반복적으로 발생하는 작업들을 plugin으로 정의해 이미 제공하고 있다. 자바 기반으로 개발하는 경우에 java 플러그인과 war 플러그인을 활용해 빌드 환경을 구축할 수 있다.

VSCode에서보면 java-library 플러그인은 다음과 같은 Tasks들을 지원한다. 자세한 내용은 [여기](https://docs.gradle.org/current/userguide/java_library_plugin.html)를 참고한다. 


## Tasks
build도 tasks 중의 하나일 뿐이다. 실행할 수 있는 tasks 들이 뭐가 있는지 살펴보자. 

```shell
 $ gracle tasks


------------------------------------------------------------
Tasks runnable from root project 'single'
------------------------------------------------------------

Build tasks
-----------
assemble - Assembles the outputs of this project.
build - Assembles and tests this project.
buildDependents - Assembles and tests this project and all projects that depend on it.
buildNeeded - Assembles and tests this project and all projects it depends on.
classes - Assembles main classes.
clean - Deletes the build directory.
jar - Assembles a jar archive containing the main classes.
testClasses - Assembles test classes.

Build Setup tasks
-----------------
init - Initializes a new Gradle build.
wrapper - Generates Gradle wrapper files.

Documentation tasks
-------------------
javadoc - Generates Javadoc API documentation for the main source code.
```

각 태스크를 실행하려면 다음과 같이 실행한다. 

```shell
$ gradle [태스크명]
```














