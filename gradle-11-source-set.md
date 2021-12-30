# Gradle (11))  SourceSets 

## SourceSet 
프로젝트를 빌드하게 되면 디폴트로 src/main/java 디렉토리의 파일이 빌드된다. 디폴트 폴더가 아닌 다른 경록의 소스를 컴파일 하는 경우에는 SrouceSets를 사용하여 경로를 변경할 수 있다.

* Java source와 resource의 논리적인 그룹들을 표현한다. 

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


