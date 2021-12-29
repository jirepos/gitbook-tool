# Gadle (6) - build.gradle #4 Task

## Task 기본 사용법


플러그인을 사용하면 미리 잘 정의된 태스크들을 사용할 수 있지만  다른 특정한 작업들을 정의할 필요가 있다. 아런 경우 task를 사용하여 커스텀 task들을 정의할 수 있다.


* Gradle의 모든 빌드 단위는 task로 정의한다. task는 Ant의 target과 같은 개념이다.
* Gradle에서는 개발자가 추가하고 싶은 빌드 단위가 있으면 새로운 task를 추가할 수 있다

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

## doFirst와 doLast 
doFirst와 doLast를 사용하하면 task사 실핼될 때 처리할 일들의 순서를  정의할 수 있다.

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
**기본 예**
```shell
task hello {
    doFirst { 
        println “Hello”
    }
    doLast { 
        println “Good Bye”
    }
}
```
**실행**
```shell
$ gradle hello

Hello
Good Bye
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


## 종속성 지정

선행되어야할 task가 있다면 먼저 실행되도록 한다.


**형식**
```shell
task 태스크명 { 
   dependsOn 다른태스크명
   // 내용
}
```

**형식**
```shell
task 태스크명(dependsOn: ‘태스크’) {
   // 내용
}

```
태스크가 여러개 있다면 배열로 정의한다. 예) [ ‘a’, ‘b’, ‘c’]



**기본 예**
```shell
task hello {
    println "hello"
}

task intro(dependsOn: 'hello' ) {
    println "intro"
}
```
**실행**
```shell
hello
intro
```
**다른 예**
```shell

task hello {
    println "hello"
}

task intro() {
    dependsOn tasks.hello
    println "intro"
}

```
tasks.hello와 같이 사용할 수도 있고 hello를 그냥 쓸 수도 있다.

**실행**
```shell
hello
intro
```
**다른 예**
```shell
task hello {
    println "hello"
}
task hello2 {
    println ("hello2")
}
task intro() {
    dependsOn hello, hello2
    println "intro"
}
```
여러개의 의존성은 콤마를 사용한다. 또는  dependsOn("hello", "hello2")와 같이 사용할 수 있다.

## 디폴트 태스크들
프로젝트를 빌드할 때 태스크를 저정하지 않으면 defaultTasks에 지정된 task들이 실행된다. 기본 태스크를 정의하는 방법을 알아본다.

**기본 태스크**
```shell
defaultTasks 'clean', 'run'
 
task clean  {
...
}
task run  {
...
}
```
멀티 프로젝트에서 각 하위 프로젝트는 자신만의 기본 태스크를 선언할 수 있다. 기본 태스크가 없으면 부모의 기본 태스크를 수행한다.

## 파라미터 전달 
태스크에 파라미터를 전달할 수 있는데 파라미터를 정의하고 사용하는 방법을 살펴본다.






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



