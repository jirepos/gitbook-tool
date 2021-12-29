# Gadle (7) - build.gradle #5 Method


task에서 처리할 로직이 복잡한 경우에 Java에서처럼 method를 생성하여 사용할 수 있다. 메서드를 정의하고 사용하는 방법을 알아본다.


**사용 예**
```shell
void printHello(String greetings) {
    println greetings
}

task hello {
    printHello("hi")
}
```

**실행**
```shell
$ gradle hello

> Configure project :
hi

BUILD SUCCESSFUL in 1s
```



