# relativePath




## 기본 상위 POM 해석 
프로젝트 구조가 다음과 같다고 가정한다. 

```
📂📂aggregator
  📂module1
    📄pom.xml  // 자식 pom.xml
  📄pom.xml   // 부모 pom.xml 
```  

module1에서 부모 POM을 지정하려면 groupId , artifactId 및 version , 이른바 GAV 좌표 를 지정하여 이를 수행할 수 있다. 


Maven은 먼저 저장소에서 검색하여 상위 POM을 확인하지 않는다. 


* 상위 폴더에 pom.xml 파일이 있고  이 파일에 위치하는 좌표가 있으면 프로젝트의 상위 POM으로 분류된다. 
* 그렇지 않으면 Maven 리포지터리에서 찾는다. 


module1의 pom.xml에 다음과 같이 설정한다. 
```xml
<artifactId>module1</artifactId>
<parent>
    <groupId>com.jirepo</groupId>
    <artifactId>aggregator</artifactId>
    <version>1.0.0</version>
</parent>
```

부모 POM은 다음과 같이 설정한다. 
```xml
<groupId>com.jirepo</groupId>
<artifactId>aggregator</artifactId>
<version>1.0.0</version>
```

만일 정보가 맞지 않으면 다음과 같은 에러가 표시된다. 
```
[ERROR]     Non-resolvable parent POM for
 Could not find artifact 
 and 'parent.relativePath' points at wrong local POM @ line 7, column 13
```



## 상위 POM의 위치 사용자 정의

module1이 aggregator의 모듈이고  module2가 module1의 자식 모듈이라고 가정한다. 

```
📂aggregator
  📂module1
    📄pom.xml  // 자식 pom.xml
  📂module2
    📄pom.xml  // 자식 pom.xml    
  📄pom.xml   // 부모 pom.xml 
```  

이 경우 module1은 module2의 상위 폴더에 없다. 상위 POM이 상위 폴더에 없으면 위치를 참조하기 위해 relativePath 태그를 사용해야 한다. 

```xml
<artifactId>module2</artifactId>
<parent>
    <groupId>com.jirepo</groupId>
    <artifactId>module1</artifactId>
    <version>1.0.0</version>
    <relativePath>../module1/pom.xml</relativePath>
</parent>
```

## 로컬 파일 확인 비활성화 

로컬 파일 검색을 건너뛰고 Maven 저장소에서 부모 POM을 직접 검색하려면 상대 경로 를 빈 값 으로 명시적으로 설정해야 한다. 

```xml
<parent>
    <groupId>com.jirepo</groupId>
    <artifactId>external-project</artifactId>
    <version>1.0.0</version>
    <relativePath/>
</parent>
```

이것은 Spring Boot 와 같은 외부 프로젝트에서 상속할 때 사용한다. 


## 설명
Maven이 Maven 저장소를 먼저 검색하여 상위 POM을 해결하지 않는다는 것을 확인했다. 오히려 로컬에서 검색하고 외부 프로젝트에서 상속할 때 이 동작을 명시적으로 비활성화해야 한다.  또한 IDE는 작업 공간의 프로젝트를 추가로 확인할 수 있으므로 외부 Maven 런타임을 사용할 때 오류가 발생할 수 있다.



## References
[Understanding Maven’s “relativePath” Tag for a Parent POM](https://www.baeldung.com/maven-relativepath)




