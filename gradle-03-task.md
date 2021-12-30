# Gadle (3) - Task


이 장에서는 Gradle 빌드 스크립트 작성의 기본 사항을 소개한다. 그러나 표준 프로젝트 설정을 시작하기 위해 이러한 개념에 대해 자세히 설명할 필요조차 없다. 대신 단계별 샘플을 통해 간단한 실습 소개를 할 수 있다.


* Gradle 7.3.3을 기준으로 작성되었다. 



## 프로젝트, 플러그인 및 작업 

Gradle이 프로젝트에서 수행할 수 있는 작업은 하나 이상의 작업으로 정의됩니다 . 작업은 빌드가 수행하는 몇 가지 원자적 작업을 나타냅니다. 이것은 일부 클래스를 컴파일하거나, JAR을 생성하거나, Javadoc을 생성하거나, 일부 아카이브를 저장소에 게시하는 것일 수 있습니다.


**일반적으로 작업은 플러그인 을 적용하여 제공 되므로 사용자가 직접 정의할 필요가 없습니다.**




플러그인을 사용하면 미리 잘 정의된 태스크들을 사용할 수 있지만  다른 특정한 작업들을 정의할 필요가 있다. 아런 경우 task를 사용하여 커스텀 task들을 정의할 수 있다.


* Gradle의 모든 빌드 단위는 task로 정의한다. task는 Ant의 target과 같은 개념이다.
* Gradle에서는 개발자가 추가하고 싶은 빌드 단위가 있으면 새로운 task를 추가할 수 있다

자세한 내용은 [빌드 스크립트 기본 사항](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#tutorial_using_tasks)를 참고한다. 


## Hello World 

build.gradle에 다음과 같이 입력한다. 

```shell
tasks.register('hello') {
    doLast {
        println 'Hello world!'
    }
}
```

다음과 같이 실행할 수 있다. 

```shell
gradle -q hello
```


## Build Script는 코드이다. 

Gradle의 build scrip9t는 Groovy와 Kotlin의 full power을 제공해준다.  아래를 보자.

```shell
tasks.register('upper') {
    doLast {
        String someString = 'mY_nAmE'
        println "Original: $someString"
        println "Upper case: ${someString.toUpperCase()}"
    }
}
```
다음과 같이 실행한다. 
```shell
> gradle -q upper
Original: mY_nAmE
Upper case: MY_NAME
```


## 작업 종속성


다른 작업에 의존하는 작업을 선언할 수 있다. 
```shell
tasks.register('hello') {
    doLast {
        println 'Hello world!'
    }
}
tasks.register('intro') {
    dependsOn tasks.hello
    doLast {
        println "I'm Gradle"
    }
}
```
실행해 보자. 

```shell
$W gradle -q intro
Hello world!
I'm Gradle
```
종속성을 추가하기 위해 해당 태스크가 존재하지 않아도 된다. 


```shell
asks.register('taskX') {
    dependsOn 'taskY'
    doLast {
        println 'taskX'
    }
}
tasks.register('taskY') {
    doLast {
        println 'taskY'
    }
}
```
실행해 보자. 
```shell
$ gradle -q taskX

> Connecting to Daemon


taskY
taskX
```
의 종속성 taskX에 taskY이전에 선언 될 수는 taskY정의된다. 작업 종속성은 작업에 종속성 추가 에서 자세히 설명 한다. 

## 유연한 작업 등록 

Groovy 또는 Kotlin의 기능은 작업이 수행하는 작업을 정의하는 것 이상으로 사용될 수 있다. 예를 들어 루프에서 동일한 유형의 여러 작업을 등록하는 데 사용할 수 있다.

```shell
4.times { counter ->
    tasks.register("task$counter") {
        doLast {
            println "I'm task number $counter"
        }
    }
}
```
실행해보자. 
```shell
$ gradle -q task1
I'm task number 1
```


**기존 작업에 동작 추가**
또는 기존 작업에 동작을 추가할 수 있다.


```shell

tasks.register('hello') {
    doLast {
        println 'Hello Earth'
    }
}
tasks.named('hello') {
    doFirst {
        println 'Hello Venus'
    }
}
tasks.named('hello') {
    doLast {
        println 'Hello Mars'
    }
}
tasks.named('hello') {
    doLast {
        println 'Hello Jupiter'
    }
}
```
실행해 보자
```shell
$ gradle -q hello
Hello Venus
Hello Earth
Hello Mars
Hello Jupiter
```
호출 doFirst하고 doLast여러 번 실행할 수 있다. 작업 작업 목록의 시작 또는 끝에 작업을 추가합니다. 작업이 실행되면 작업 목록의 작업이 순서대로 실행된다. 


## Ant 태스크 사용
Ant 작업은 Gradle의 일급 시민이다. Gradle은 단순히 Groovy에 의존하여 Ant 작업에 대한 탁월한 통합을 제공한다. Groovy는 환상적인 AntBuilder. Gradle에서 Ant 작업을 사용하는 것은 build.xml파일 에서 Ant 작업을 사용하는 것만큼 편리하고 강력 하다. 그리고 Kotlin에서도 사용할 수 있다. 아래 예에서 Ant 작업을 실행하는 방법과 Ant 속성에 액세스하는 방법을 배울 수 있다.

```shell
tasks.register('loadfile') {
    doLast {
        def files = file('./antLoadfileResources').listFiles().sort()
        files.each { File file ->
            if (file.isFile()) {
                ant.loadfile(srcFile: file, property: file.name)
                println " *** $file.name ***"
                println "${ant.properties[file.name]}"
            }
        }
    }
}
```


실행해 보자. 
```shell
$ gradle -q loadfile
```


## Methods 사용
Gradle은 빌드 로직을 구성하는 방법을 확장한다. task에서 처리할 로직이 복잡한 경우에 Java에서처럼 method를 생성하여 사용할 수 있다. 메서드를 정의하고 사용하는 방법을 알아본다.


```shell
void printHello(String greetings) {
    println greetings
}

tasks.register('hello') {
    printHello("hello")
}
```
실행해 보자.
```shell
$ gradle -q hello
hello
```


## doFirst와 doLast 
doFirst와 doLast를 사용하하면 task가 실핼될 때 처리할 일들의 순서를  정의할 수 있다.

**형식**
```shell
task 태스크명 {
    doFirst { 
        // 태스크 내에서 doLast보다 먼저 처리할 내용
    }
    doLast { 
        // 태스크 내에서 doFirst보다 나중에 처리할 내용
    }
}
```
다음과 같이 작성한다.
```shell

tasks.register('hello') {
    doFirst  { 
        println "doFirst"
    }
    doLast { 
        println "doLast"
    }
}
```


실행해 보자. 
```shell
$ gradle -q hello
doFirst
doLast
```





## 기본 작업 

Gradle을 사용하면 다른 작업이 지정되지 않은 경우 실행되는 하나 이상의 기본 작업을 정의할 수 있다.


```shell
defaultTasks 'clean', 'run'

tasks.register('clean') {
    doLast {
        println 'Default Cleaning!'
    }
}

tasks.register('run') {
    doLast {
        println 'Default Running!'
    }
}

tasks.register('other') {
    doLast {
        println "I'm not a default task!"
    }
}
```


실행해 보자. 

```shell
gradle -q
> gradle -q 
기본 청소! 
기본 실행!
```

이것은 'gradle clean run'을 실행하는 것과 같습니다. 

다중 프로젝트 빌드에서 모든 하위 프로젝트는 고유한 특정 기본 작업을 가질 수 있다. 하위 프로젝트가 기본 작업을 지정하지 않으면 상위 프로젝트의 기본 작업이 사용된다.(정의된 경우)











**형식** 
```shell
task 태스크명 {
    // 내용
}
```
**기본 예**
```shell
task hello {
   println 'Hello world!'
}
```
실행 
```shell
$ gradle hello

Hello world!
```




## 다른 Task 호출 
task내에서 다른 태스크를 실행하는 것은 오류가 발생한다. 지원을 하는 것인지 아닌지 아직 확인되지 않았음


**형식**
```shell
task 태스크명 {
   // 내용
   tasks.다른태스크명.excute()
}
task 다른태스크명 {
    // 내용
}

```
**기본 예**
```shell
task hello { 
    println “hello”
    tasks.otherTask.execute()
}
task otherTask { 
    println “Other Task”
}

```
**실행**
```shell
$ gradle hello

Hello
Good Bye
```


## 파라미터 전달 
태스크에 파라미터를 전달할 수 있는데 파라미터를 정의하고 사용하는 방법을 살펴본다.

> 이전 버전의 예제이므로 task 정의는 7.3.3 버전의 register를 사용하는 방식으로 변경한다. 
> 
**형식**
```shell
task 태스크명 { 
     println(“입력받은 변수:” + x) 
}
```
**기본 예**
단순히 변수를 선언하면 된다.

```shell
task hello {
    println(“입력값:" + x)
}

```
**실행**
```shell
$ hello –Px=“김”

입력값: 김
```
-P 이후에 변수명을 쓰고 값을 설정하여 파라미터를 전달할 수 있다.


## Task를 확장하는 방법
Gradle은 표준으로 다양한 태스크가 포함되어 있는데 이것들은 DefaultTask라는 클래스를 상속한 클래스로 제공된다.  이 DefaultTask 상속 클래스는 자신의 태스크를 만들어 커스텀을 할 수 있다.


**형식**
```shell
class 클래스명 extends DefaultTask {
    // 필드 선언 
    // 메서드 선언
    void 메서드명(인자) { 
    }
    @TaskAction
     void 메서드() {
     }
}
```
태스크로 수행될 메서드는 @TaskAction 어노테이션을 붙인다.

주어진 숫자까지 반복하여 합계를 구하는 타스크를 정의해 보자. DefaultTask를 상속 받는다. 멤버 변수를 선언하고 멤버 변수에 값을 설정할 메서드를 생성한다.  실제 Task로 실행될 메서드는 @TaskAction 어노테이션을 붙인다.

**Calc 커스텀 클래스**
```groovy
class Calc extends DefaultTask  {
    private int num
    private String op

    void num(p1) {
        num = p1
    }
    void op(p1) {
        op = p1
    }
    @TaskAction
    void calc() {
        int total = 0
        for(def i in 1..num) {
            total += i
        }
        println("Total: ${total}")
    }
}
```
이제 Calc 클래스를 사용하여 Task를 정의하는 방법을 알아본다.

**형식**
```shell
task 태스크명(type: 클래스) {
    // 수행할 처리 
}
```
task의 인자로 type을 사용하여 Calc를 설정한다.  num 메서드와 op 메서드를 호출한다. 이렇게 하면 인자로 전달된 Calc 클래스 멤버 필드에 값을 할당하게 된다. group에 그룹명을 정의한다(optional). description에 설명을 작성한다(optional).

```shell
task mytask(type: Calc ){
    group "mytaskgroup"
    description "Test Task"
    num 10
    op 'count'
}
```
task의 인자로 type을 사용하여 Calc를 설정한다.  num 메서드와 op 메서드를 호출한다. 이렇게 하면 인자로 전달된 Calc 클래스 멤버 필드에 값을 할당하게 된다.

**실행** 

gradle mytask를 실행하면 다음과 같은 결과를 얻는다.
```shell
> Task :mytask
Total: 55
```

## JavaExec 클래스 
Gradle에서 제공하는 주요 태크스 클래스에 대한 사용방법을 알아본다.  JavaExec는 Java 프로그램의 실행을 위한 태스크를 구현하는 클래스이다. JavaExec이외에 커맨드를 실행하는 Exec, 복사를 하는 Copy 등이 있다.

**형식**
```shell
task 태스크명(type: JavaExec) {
    group  ‘그룹이름’
    description ‘설명’ 
    main ‘패키지경로.메인클래스명’  // 실행할 클래스명
    classpath sourceSets.main.runtimeClasspath  // 클래스 패스 경로
    args ‘hello’  // 인자를 전달할 때
    args ‘good’  // 인자가 여러개 이면 아래에 써준다.
    jvmArgs ‘값1,값2’ // Java VM에 전달할 인자를 써준다
}
```
자세한 사항은 아래의 URL에서 확인한다.
https://gradle.github.io/kotlin-dsl-docs/api/org.gradle.api.tasks/-java-exec/index.html

**JavaExcec 예**
apprun task를 실행하면 org.example.App.class가 실행된다.

```shell
task apprun(type: JavaExec ){
    group "mytaskgroup"
    description "Run Java"
    main 'org.example.App'
    classpath sourceSets.main.runtimeClasspath
}
```

https://www.baeldung.com/gradle-run-java-main
https://araikuma.tistory.com/465?category=782572



