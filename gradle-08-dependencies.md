# Gradle (8) 의존성 관리 

## Local 라이브러리 모듈 의존성 설정하기 
프로젝트를 구성할 때 Local에 정의된 모듈의 의존을 정의하는 방법이다. Local의 모듈 혹은 프로젝트를 끌어와서 사용하는 경우에는 settings.gradle 파일에 설정 된 "include:"로 정의한 프로젝트의 이름과 같아야 한다. 

**settings.gralde**
```shell
include 'app', 'core' 
```


**app/build.gradle**
```shell
dependencies {
    implementation  project(':core')
}
```

## Local 바이너리 라이브러리 의존성 설정하기
```shell
implementation fileTree(dir: 'libs', include: ['*.jar'])
```

패키지화되어 있는 라이브러리 모듈의 의존을 정의하는 방법이다. Gradle은 build.gradle 파일을 기준으로 루트 경로가 정의됩니다. 위의 설정에서는 "[프로젝트]/libs/" 폴더 내의 JAR 파일들을 모두 의존하게 된다. 

* build.gradle이 있는 위치가 project root 

다음은 특정 jar 파일들만 의존한다. 
```shell
implementation files('libs/my.jar', 'libs/other.jar')
```

## 원격 라이브러리 의존성 설정
```shell
implementation 'org.springframework.boot:spring-boot-starter'
```
외부에 정의되어 있는 바이너리 라이브러리 의존성을 설정합니다. 라이브러리가 Local에 존재하지 않는다면 정의된 원격 저장소에서 라이브러리를 다운받아 빌드할 수 있도록 한다. 






