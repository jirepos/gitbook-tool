# Gadle (5) - Properties
 
 

## 프러퍼티 사용 
build script 파일에서 프러퍼티를 사용할 수 있는 프러퍼티들에 대해서 알아본다. 


* -D프라퍼티명=값으로 시스템 프라퍼티를 추가할 수 있다.
* gradle.properties를 통해 프라퍼티를 추가할 수 있다.
* $USER_HOME/.gradle/gradle.properties 혹은
* 프로젝트홈/gradle.properties
* **$USER_HOME에 있는 것이 우선한다.**
* 여기 지정된 값을 project 객체를 통해 접근할 수 있다.
* -P프라퍼티명=값으로 project 객체에 프라퍼티를 추가한다.
* 환경변수 ORG_GRADLE_PROJECT_프라퍼티이름=값으로 project 객체에 프라퍼티를 추가한다.
* 시스템 프라퍼티 org.gradle.project.프라퍼티이름=값으로 project 객체에 프라퍼티를 추가한다.
* gradle.properties의 프라퍼티 중에 systemProp.으로 시작하는 프라퍼티는 시스템 프라퍼티로 변환된다.


**gradle.properties** 
```properties
gradlePropertiesProp=gradlePropertiesValue
systemPropertiesProp=shouldBeOverWrittenBySystemProp
envPropertiesProp=shouldBeOverWrittenByEnvProp
systemProp.system=systemValue
```
**build.gradle**
```shell
task printProps << {
    println commandLineProjectProp
    println gradlePropertiesProp
    println systemProjectProp
    println envProjectProp
    println System.properties['system']
```
**실행 결과**
```shell
> gradle -q -PcommandLineProjectProp=commandLineProjectPropValue -Dorg.gradle.project.systemProjectProp=systemPropertyValue printProps
commandLineProjectPropValue
gradlePropertiesValue
systemPropertyValue
envPropertyValue
systemValue
```


**프로젝트 프러퍼티 검사**

* 프로젝트 프라퍼티는 빌드 스크립트에서 프라퍼티 이름으로 바로 접근 가능하다. 하지만 프라퍼티가 존재하지 않으면 예외가 발생한다.
* hasProperty('propertyName')으로 프라퍼티의 존재 여부를 검사할 수 있다.


### project 클래스에서 사용할 수 있는 변수들

| property | 설명 |
|--|--|
| allprojects	| 이 프로젝트와 그것의 서브프로젝트를 포함하는 set.	[root project 'gradle-spring-web'] |
| buildDir |	이 프로젝트의 빌드 디렉토리. 	E:\gradle-spring-web\build |
| buildFile	| 이 프로젝트를 위한 빌드 스크립트. 	E:\gradle-spring-web\build.gradle |
| childProjects | 	이 프로젝트의 직접 자식들 |
| configurations | 	이 프로젝트의 configurations | 
| defaultTasks | 	이 프로젝트의 디폴트 태스크들의 이름들 | 
| description	 | 이 프로젝트의 설명 | 
| name | 	이 프로젝트의 이름 | 
| parent | 	이 프로젝트의 부모 | 
| path | 	이 프로젝트의 경로(path) | 
| projectDir | 	프로젝트 빌드 파일을 포함하는 경로. 	E:\gradle-spring-web  | 
| properties | 	이 프로젝트의 properties | 
| rootDir	 | 이 프로젝트의 root diredctory. 	E:\gradle-spring-web | 
| version	 | 이 프로젝트의 버전 | 




### java plugin이 추가한 properties


| property | 설명 |
|--|--|
| archivesBaseName | 	archive files을 위한 기본이름 | 
| distsDir	 | TAR와 ZIP archives을 생성하여 놓을 디렉토리.	E:\gradle-spring-web\build\distributions  | 
| distsDirName | 	distributions directory을 위한 이름. | 
| distributions | docsDir	모든 문서들을 사용될 디렉토리를 가리키는 파일을 반환한다.	E:\gradle-spring-web\build\docs | 
| docsDirName	 | docs 디렉토리의 이름 | 
| libsDir	 | JAR와 WAR 파일이 생성될 디렉토리 | 
| libsDirName	 | libs 디렉토리의 이름  | 
| sourceCompatibility | 	java sources를 컴파일 하는 데 사용 | 
| sourceSets | 	source sets container | 
| targetCompatibility | 	Java sources를 컴파일 하는 데 사용하는 targe compatibility | 



### war plugin이 추가한 properties
| property | 설명 |
|--|--|
| webAppDir	| web application directory | 
| webAppDirName	| web application directory의 이름 |


### 추가 속성 정의하기

앞에서 사용할 수 있는 프러퍼티들 이외에 추가적인 프러퍼티가 필요할 수 있다. 모든 추가 속성은 ext namespace를 통해서 정의되어야 한다.

```shell
ext.javaVersion = '1.7' // 한개씩 선언
ext {
    // 여러개 한꺼번에 선언
    springVersion = '3.1.0.RELEASE'
    emailNotification = 'build@master.org'
}
 
// 가변 Key, 가변 값 형태로 코드를 통해 프라퍼티를 추가할 때는 아래 방식을 사용한다.
project.ext['keyname'] = 'value'
 
task hello << {
    println "javaVersion : ${javaVersion}"
    println "springVersion : ${springVersion}"
    println "emailNotification : ${emailNotification}"
}
```



