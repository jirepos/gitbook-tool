# Maven Multi Module Project 

회사 내에서 여러 프로젝트에 공통으로 제공할 라이브러리와 의존성의 제약을 두기 위해서 프로젝트를 생성하고 이 라이브러리를 사용하여 프로젝트를 생성하는 방법을 간단히 정리한다. 

* 두 개의 프로젝트를 생성한다. 
  * 의존성관리 및 라이브러리 제공 공통 프로젝트
  * 응용 모듈 프로젝트
* 응용 모듈 프로젝트는 공통 프로젝트의 POM을 상속 받는다. 



## Project 구조
### 공통 프로젝트 
```shell
 📁project_root 
    📁dependency module
       📄pom.xml 
    📁library module
       📁src
          📁main
            📁java
       📄pom.xml 
    📄pom.xml 
```
### 응용 모듈 프로젝트 

```shell
📁project_root 
  📁src
     📁main
        📁java
  📄pom.xml 
```


## pom.xml 설정

### 공통 프로젝트 
**project root**
두 개의 모듈을 정의하고 버전을 명시한다. packaging 형식은 pom으로 한다. 
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.my</groupId>
  <artifactId>my-framework</artifactId>
  <version>1.0.0</version>
  <packaging>pom</packaging>
  <modules>
  	<module>my-dependencies</module>
  	<module>my-libs</module>
  </modules>
</project>
```

**dependency module**
parent 정보를 설정하고  packaging 형식은 pom으로 한다. 의존성을 관리한다. 

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>com.my</groupId>
		<artifactId>my-framework</artifactId>
		<version>1.0.0</version>
	</parent>
	<artifactId>my-dependencies</artifactId>
	<packaging>pom</packaging>
	<dependencies>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<scope>provided</scope>
			<version>3.0.1</version>
		</dependency>
		<dependency>
			<groupId>taglibs</groupId>
			<artifactId>standard</artifactId>
			<version>1.1.2</version>
		</dependency>
  </dependencies>
</project>
```


**library module**
dependencies 모듈을 parent로 설정한다. relativePath 에 상대 경로로 dependencies 경로를 설정한다. 

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>com.my</groupId>
    <artifactId>my-dependencies</artifactId>
    <version>1.0.0</version>
    <relativePath>../my-dependencies</relativePath>
  </parent>
  <artifactId>my-framework-parent</artifactId>
</project>  
```


### 응용 모듈의 프로젝트 
parent에 dependencies 모듈을 설정한다.  packging 형식은 용도에 맞게 jar나 war로 설정한다. 
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>com.my</groupId>
		<artifactId>my-dependencies</artifactId>
		<version>1.0.0</version>
	</parent>
	<artifactId>blog</artifactId>
	<packaging>war</packaging>
</project>  
```


## repositories 설정 
### dependency 모듈 
dependencies 모듈에서 의존성을 관리하기 때문에 \<repository\>를 설정해야 한다. 
```xml
	<repositories>
		<repository>
			<id>mvn2</id>
			<url>https://repo1.maven.org/maven2/</url>
			<releases>
				<enabled>true</enabled>
			</releases>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>
  </repositories>    
```
### 응용 모듈 
응용 모듈에서만 사용하는 의존성이 있을 경우에는 repository 설정을 추가해야 한다. 
```xml
	<repositories>
		<repository>
			<id>mvn2</id>
			<url>https://repo1.maven.org/maven2/</url>
			<releases>
				<enabled>true</enabled>
			</releases>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>
  </repositories>    
```


## 플러그인 
메이븐이 제공하는 모든 기능은 플러그인 기반으로 동작한다.  메이븐 페이즈 또한 플러그인을 통해 실행된다. plugin은 \<plugins\>와 \<pluginManagement\> 두 곳에서 설정할 수 있는데 pluginManagement에 설정하면 부모 pom을 상속받는 하위 프로젝트에서 사용할 수 있다. 


### depenency 모듈에 플러그인 설정 
dependencies 모듈의 pom.xml 파일에  기본적으로 사용할 플러그인 들을 pluginManagement에 설정한다. 

```xml
	<build>
		<pluginManagement>
			<plugins>
				<plugin>
					<groupId>org.apache.maven.plugins</groupId>
					<artifactId>maven-clean-plugin</artifactId>
					<version>3.0.0</version>
					<executions>
						<execution>
							<id>auto-clean</id>
							<phase>initialize</phase>
							<goals>
								<goal>clean</goal>
							</goals>
						</execution>
					</executions>
				</plugin>
				<plugin>
					<groupId>org.apache.maven.plugins</groupId>
					<artifactId>maven-assembly-plugin</artifactId>
					<version>2.4</version>
					<configuration>
						<descriptorRefs>
							<descriptorRef>jar-with-dependencies</descriptorRef>
						</descriptorRefs>
					</configuration>
					<executions>
						<execution>
							<id>make-assembly</id> <!-- this is used for inheritance merges -->
							<phase>package</phase> <!-- bind to the packaging phase -->
							<goals>
								<goal>single</goal>
							</goals>
						</execution>
					</executions>
				</plugin>
				<plugin>
					<groupId>org.apache.maven.plugins</groupId>
					<artifactId>maven-compiler-plugin</artifactId>
					<configuration>
						<source>${target.jdk}</source>
						<target>${target.jdk}</target>
						<encoding>UTF-8</encoding>
					</configuration>
				</plugin>

				<plugin>
					<groupId>org.apache.maven.plugins</groupId>
					<artifactId>maven-surefire-plugin</artifactId>
					<version>2.16</version>
					<configuration>
						<skipTests>true</skipTests>
					</configuration>
				</plugin>
			</plugins>
		</pluginManagement>
	</build>
```

### 응용 모듈에 plugin 추가 
응용 모듈에서 빌드 시에 사용할 plugin을 추가하거나 부모에서 설정한 plugin을 오버라이드할 경우에 plugin을 추가한다. 


```xml
  <build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-clean-plugin</artifactId>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-jar-plugin</artifactId>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-surefire-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
```






