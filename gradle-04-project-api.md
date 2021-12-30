# Gadle (4) - Project API


Gradle은 빌드 스크립트로 Groovy DSL을 사용하고 Gradle DSL은 Gradle 도메인에 적용되기 위한 언어로 Groovy로 만든 Gradle 만을 위한 새로운 언어이다. 

Project객체는 프로젝트와 build.gradle 파일간에 일대일 대응 관계에 있으며 build.gradle은 Project의 스크립트 표현으로 이해하면 된다. 

Project 객체에서는 그레이들의 모든 기능에 대하여 프로그래밍 방식으로 접근할 수 있도록 각종 속성 및 메서드 등을 지원하고 있다. 


Gradle Project은 다음과 같이 빌드에 참여할 각 프로젝트에 대한 개체를 어셈블 합니다.


* Settings빌드에 대한 인스턴스를 만든다. 
* settings.gradle스크립트가 있는 경우 Settings구성할 개체에 대해 스크립트를 평가한다. 
* 구성된 Settings개체를 사용하여 Project인스턴스 계층을 생성한다.
* 마지막으로 해당 Project build.gradle 파일이 있는 경우 프로젝트에 대해 실행하여 각각 을 평가한다. 프로젝트는 하위 프로젝트보다 먼저 평가되도록 광범위한 순서로 평가된다. 



## Project 객체의 프러퍼티에 접근하기

```shell
// build.gradle

project.version = "1.0.0"  // project property 값 설정

tasks.register('mytest') {
    doLast {
        println project.getVersion()
    }
}
```

Project objec는 표준 프러퍼티들을 제공한다.

![](.gitbook/assets/gradle/2021-12-29-12-19-14.png)


값이 설정된 프러퍼티의 값은 다음과 같이 읽을 수 있다. 

```shell
println project.getBuildDir()
```        



## 지역변수 사용 
def 를 사용하여 지역변수를 정의할 수 있다.

```shell
//build.gradle

def myString = "hello"

tasks.register('mytest') {
    doLast {
        println myString 
    }
}

```





## 임의의 객체 사용
> **여기서 부터는 다시 정리할 필요가 있다.**


읽기 쉬운 방식으로 임의의 객체들을 다음과 같이 생성할 수 있다.
```shell
import java.text.FieldPosition

tasks.register('configure') {
    doLast {
        def pos = configure(new FieldPosition(10)) {
            beginIndex = 1
            endIndex = 5
        }
        println pos.beginIndex
        println pos.endIndex
    }
}
```

## Groovy 
gradle은 Groovy를 사용한다.  Groovy의 자세한 문법은 아래의 URL을 참고한다.

https://docs.groovy-lang.org/latest/html/documentation/index.html#_closures

**Properties 사용**
```shell
project.getVersion() //아래와 동일
project.version

project.setVersion('1.0.1') //아래와 동일
project.version = '1.0.1'
```
build.gradle에서 메서드를 정의하고 호출하는 방법을 살펴본다.

**Methods 사용**

파라미터가 한 개인 printInfo() 메서드를 정의하고 호출하는 방법을 보여준다.

```shell
void printInfo(String info) {
    println info;
}
task hello {
    printInfo project.name
}
```
메서드 호출은 괄호 없이 호출할 수 있다.

```shell
task hello {
    printInfo project.name
}
```

인자가 없는 메서드는 괄호를 사용해야 한다.
```shell
void printInfo() {
    println project.name
}
task hello {
    printInfo()
}
```
파라미터가 여러개인 메서드를 정의하고 호출하는 방법을 살펴본다.
```shell
void printInfo(String prefix, String info) {
    println(prefix +  info)
}
task hello {
    printInfo("Hello ", "World")
}
```

메서드 호출은 괄호 없이 호출할 수 있다. 파라미터는 콤마(,)로 구분한다.
```shell
printInfo "Hello ", "World"
```
블록 또한 메서드이며, 마지막 인자에 대한 특별한 타입일 뿐이다.

**Block 사용**

블록은 빌드 요소의 여러 측면을 한번에 구성하기 위한 메커니즘이다. configuratio을 중첩하는 방법을 제공함으로써 구조화된 데이터 형식을 나타내게 해준다. 

* 특정한 signature를 가진 메서드로 구현된다.
* 규정되지 않은 메서드 및 property 대상(delegate 대리인)을 변경할 수 있다.

**Block 메서드 signature**
* 적어도 하나의 인자가 필요하다
* 마지막 인자는 groovy.lang.Closure type이나 org.gradle.api.Action type이어야 한다.

예를 들어, Project.copy(Action)은 이런 요구 사항과 일치하므로 다음 구문을 사용할 수 있다.

```shell
copy {
    into "$buildDir/tmp"
    from 'custom-resources'
}
```
클로저는 객체의 동작을 주 목적으로 하는 일종의 코드 블록이며 또한 클로저는 하나의 객체이다.  인자를 받고 값을 리턴할 수 있다는 점에서 메서드 처럼 동작한다. 그러나 메서드 처럼 메서드명과 시그니처를 선언하지 않아도 동작할 수 있다. 익명의 코드 블럭이라고 생각하면 된다.

**Closure**

메서드 호출에서 콜로저 인자를 전달하는데, 전달된 값의 이름이 없어도 “it”를 만들어 준다. 
```shell
// closuere 선언
def multiple = {
    return it *2
}

task hello {
     // closure 호출
    [2,4,8].each {
        println "${it} * 2 = ${multiple(it) }"
    }
}
```
**클로저의 리턴 구문**
* 로저가 실행하다 마지막 문장을 평가하고 나면 그 결과 값이 리턴된다.
* 이것을 end return이라고 하는데 return 키워드는 써도 되고 안써도 된다.
* 명시적으로 수행중 리턴을 해줘야 할 경우  return을 적어줘야 한다.

클로저의 위임에 대해서 더 알아보려면 다음의 URL을 참고한다.

https://blog.daum.net/stshms/7

Groovy DSL에 대해서 더 알아보려면 다음의 URL을 참고한다.

https://www.tutorialspoint.com/groovy/groovy_dsls.htm
















