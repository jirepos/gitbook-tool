# Gadle (88) - How-To


## 디렉토리 생성 
여러 태스크에서 특정 디렉토리를 필요로 할 경우에는, 디렉토리 생성 태스크를 만들고 다른 태스크가 그 태스크에 의존하도록 만든다.

```shell
classesDir = new File('build/classes')
task resources << {
    classesDir.mkdirs()
    // do something
}
task compile(dependsOn: 'resources') << {
    if (classesDir.isDirectory()) {
        println '필요한 디렉토리가 존재하네요.'
    }
    // do something
}
```

### 외부 빌드 스크립트로 부터 프로젝트 구성하기

외부 *.gradle 빌드 스크립트를 만들어서 불러올 수 있다.

build.gradle에서 other.gradle을 불러온다. 

```shell
apply from: ‘other.gradle'
```

**other.gradle** 
```shell
println "configuring $project"
task hello << {
    println 'hello from other script'
}
```
## 임의의 객체 구성하기
configure 메서드로 임의의 객체를 구성할 수 있다. 
```shell
task configure << {
    pos = configure(new java.text.FieldPosition(10)) {
        beginIndex = 1
        endIndex = 5
    }
    println pos.beginIndex
    println pos.endIndex
}
```
```shell
task configure << {
    pos = new java.text.FieldPosition(10)
    // 외부 스크립트 적용
    apply from: 'other.gradle', to: pos
    println pos.beginIndex
    println pos.endIndex
}
```






