# Maven 문제 해결 

## 의존성 문제 
### ojdbc6 문제 
Maven으로 ojdbc6를 설정 할 때 pom 에러가 나는 경우가 많은데 이경우 별도의 Maven Repository를 설정을 해 주어야 한다.

```xml
<dependency>
    <groupId>oracle</groupId>
    <artifactId>ojdbc6</artifactId>
    <version>11.2.0.3</version>
</dependency>
```
```xml
<repositories>
  <repository>
    <id>oracle</id>
    <name>ORACLE JDBC Repository</name>
    <url>http://www.datanucleus.org/downloads/maven2/</url>
  </repository>
</repositories>
```

