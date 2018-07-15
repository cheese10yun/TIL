# Coveralls 연동


## Repsotory 추가
![](https://i.imgur.com/jciSIwL.png)



## pom.xml
```xml
<build>
	<plugins>
		...
      <plugin>
        <groupId>org.eluder.coveralls</groupId>
        <artifactId>coveralls-maven-plugin</artifactId>
        <configuration>
            <repoToken>발급 받은 토큰</repoToken>
        </configuration>
      </plugin>

      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>cobertura-maven-plugin</artifactId>
        <configuration>
            <format>xml</format>
            <maxmem>256m</maxmem>
            <!-- aggregated reports for multi-module projects -->
            <aggregate>true</aggregate>
            <check/>
        </configuration>
      </plugin>
			...
  </plugins>
</build>
```
