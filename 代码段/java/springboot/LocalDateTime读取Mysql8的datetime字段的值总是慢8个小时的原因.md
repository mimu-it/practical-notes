# LocalDateTime读取Mysql8的datetime字段的值总是慢8个小时的原因

原因是驱动版本问题

我的mysql版本是8.0.29

system_time_zone是 UTC，time_zone是SYSTEM， 通过命令show variables like '%time_zone%'获取

开发电脑是mac系统，时区是东8区

当我使用mysql.mysql-connector-java的版本是5.1.49时，serverTimezone=GMT%2B8完全没有效果，从数据库查出来的LocalDateTime的值总是小8个小时

当使用8.0.33版本时，可以获取相同值

数据库连接参数为：

```yaml
url: jdbc:mysql://localhost:3310/bone_sample?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8&useSSL=false&allowPublicKeyRetrieval=true
```

