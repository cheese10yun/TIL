# 유저 생성

```sql
create user 'user-name'@'%' identified by 'password-value';
grant all privileges on *.* to 'user-name'@'%';
flush privileges;
```