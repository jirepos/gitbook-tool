# Gadle (8) - build.gradle #6 Plugin

## Java 플러그인 

**java plugin 사용하기**
```shell
apply plugin: 'java'
```
**빌드하기**
```shell
$ gradle build
:compileJava
:processResources
:classes
:jar
:assemble
:compileTestJava
:processTestResources
:testClasses
:test
:check
:build

BUILD SUCCESSFUL in 0s
6 actionable tasks: 6 executed
```

### 다른 유용한 플러그인들 
**clean**
build 디렉토리를 삭제한다.


**assemble**
컴파일을 하고 코드를 jar로 만든다.   그러나 unit test를 실행하지 않는다.

> 다른 플러그인들은 이 태스크에 다른 산출물을 추가한다. 만일 War plugin을 사용하면, 이 캐스크는 또한 WAR file을 빌드할 것이다. 


**check**
컴파일을 하고 코드를 test한다.


**sourceSets**


* Java 플러그인에는 Source Set이라는 개념이 들어가 있으며, 이는 함께 컴파일과 실행되는 소스 파일들의 그룹을 뜻함.
* 소스 셋에는 자바 소스 파일과 리소스 파일들이 들어감. 다른 플러그인들이 그루비나 스칼라 소스를 추가할 수 있음.
* 소스 셋은 컴파일 클래스패스와 런타임 클래스패스와 관련됨.
* 소스 셋의 목적은 소스들를 논리적 그룹으로 묶고 그 목적을 설명하는 데 있음. 통합 테스트용 소스셋, API 인터페이스 클래스들, 구현체 클래스들 형태로 구분 가능.
* 기본 Java Source Set 
  *  main : 실제 작동 소스코드. 컴파일해서 JAR 파일로 들어감.
  *  test : 단위 테스트 소스코드. 컴파일해서 JUnit이나 TestNG로 실행.


java plugin의 기본 디렉토리 구조는 메이븐 빌드 툴과 똑같다. 따라서 메이븐 기반 프로젝트를 gradle 기반으로 쉽게 변경할 수 있다. 하지만 이미 개발자에게 익숙한 디렉토리 구조가 존재할 수 있으며, 다른 빌드 툴 기반으로 생성된 프로젝트가 있는 경우에는 기본 디렉토리를 변경하는 것이 가능해야 한다. java plugin 이 같은 유연성을 보장하기 위하여 기본 설정을 변경하고자 한다면 하위 설정 파일에서 재정의하면 된다. 
만약 기본 디렉토리가 아닌 다른 디렉토리를 사용하고 싶다면 build.gradle 설정 파일에서 변경하면 된다. 예를 들어 기본 디렉토리를 다음과 같이 변경하고자 한다.


* src/main/java =\> src/java: 3단계의 디렉토리를 2단계로 줄임.
* src/main/resources =\> src/resouces: 테스트 소스에서 사용할 자원이 없을 것으로 판단해 src/resources 하나로 통합
* src/test/java =\> src/test: 3단계의 디렉토리를 2단계로 줄임.

**소스 디렉터리 경로 변경**

```shell
sourceSets {
    main {
        java {
            srcDir 'src/java'
        }
        resources {
            srcDir 'src/resources'
        }
    }
    test {
        java {
            srcDir 'test/java'
        }
    }
}
```

**특정 패키지의 클래스를 제외하고 패키징하기**
```shell
apply plugin: 'java'

sourceSets {
  main {
    java {
      exclude 'some/unwanted/package/**'
    }
  }
}
```
리소스 output 디렉터리 변경하기
```shell
apply plugin: 'java'

sourceSets {
  main {
    //if you truly want to override the defaults:
    output.resourcesDir = file('out/bin')
    // Compiled Java classes should use this directory
    java.outputDir = file('out/bin')  <-- 동작하지 않음
  }
}
```
**sourceSets의 기본 프러퍼티들**


| 프라퍼티 이름 | Type|	기본값	| 설명 |
|---|----|----|----| 
| name	 | String(read-only) | 	not null | 	소스셋 이름 | 
| output | 	SourceSetOutput	 | not null | 	소스셋의 출력 디렉토리. 컴파일된 클래스와 리소스를 저장할 곳 | 
| output.classesDir | 	File | 	buildDir/classes/name | 	소스셋의 클래스를 생성할 디렉토리 | 
| output.resourcesDir | 	File	 | buildDir/resources/name	 | 소스셋의 리소스를 생성할 디렉토리 | 
| compileClasspath |	FileCollection	 |compileSourceSet configuration |	소스를 컴파일할 때 사용할 클래스패스 |
| runtimeClasspath |	FileCollection	 |output + runtimeSourceSet |configuration	클래스 실행시의 클래스패스 |
| java |	SourceDirectorySet(read-only)	 | not null	 |소스셋의 자바 소스파일들. *.java 파일만 포함호며 그 외 파일은 제외된다. |
| java.srcDirs |	Set<File>, Project.files()에서 사용할 수 있는 모든 값 |	[projectDir/src/name/java]	 | 자바 소스 파일을 포함하고 있는 소스 디렉토리들,srcDir()로 추가가능 |
| resources	 | SourceDirectorySet(read-only)	 |not null |	소스셋의 리소스들. *.java 파일은 제외된다. 플러그인에 때라 제외되는 목록이 추가된다.  |
| resources.srcDirs	 |Set<File>, Project.files()에서 사용할 수 있는 모든 값	 |[projectDir/src/name/resources]	 |리소스를 포함하고 있는 소스 디렉토리들 |
| allJava	 | SourceDirectorySet(read-only)	 |java	 |모든 *.java 파일들. 플러그인에 따라 더 추가 될 수 있음 |
| allSource	 | SourceDirectorySet(read-only) |	resources + java |	모든 소스 파일들(리소스 + *.java). 플러그인에 따라 더 추가 될 수 있음. |







**sourceSets의 정보 출력하는 task**
SourceSets의 정보를 출력하는 간단한 task를  살펴보자


```shell
task printSourceSetInformation(){

    doLast{
        sourceSets.each { srcSet ->
            println "["+srcSet.name+"]"
            print "-->Source directories: "+srcSet.allJava.srcDirs+"\n"
            print "-->Output directories: "+srcSet.output.classesDirs.files+"\n"
            print "-->Compile classpath:\n"
            srcSet.compileClasspath.files.each {
                print "  "+it.path+"\n"
            }
            println ""
        }
    }
}
```


**runtimeClasspath를 출력하는 방법**
SourceSets의runtime 시의 classpath 정보를 확인하려면 다음과 같이 한다.

```shell
 sourceSets.main.runtimeClasspath.files.each {
        println it.path
 }
```

## WAR 플러그인
war plugin에서 웹 자원을 관리하기 위한 기본 디렉토리는 src/main/webapp이다. 이 또한 메이븐 빌드 툴의 기본 디렉토리와 동일하다. war plugin의 기본 디렉토리를 변경하려면 다음과 같이 webAppDirName을 지정하면 된다.

```shell
configure {
    webAppDirName = 'webapp'   
}
```

