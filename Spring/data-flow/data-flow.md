
```
stream create --name mysqlstrea3 --definition "http --server.port=9010 | jdbc --tableName=names --columns=name,lastName,firstName --spring.datasource.driver-class-name=org.mariadb.jdbc.Driver --spring.datasource.url=jdbc:mysql://mysql:3306/dataflow --spring.datasource.username=root --spring.datasource.password=rootpw" --deploy
```

```
http post --contentType 'application/json;charset=UTF-8' --target http://172.24.0.2:9010 --data "{\n  \"name\": \"yun\",\n  \"firstName\": \"100\",\n  \"lastName\": \"200\"\n}"

http post --contentType 'application/json;charset=UTF-8' --target http://172.24.0.2:9011 --data "{\n  \"name\": \"www\",\n  \"firstName\": \"100\",\n  \"lastName\": \"200\"\n}"
```