# 카프카 디비지움 Source 커넥트

## 커넥터 등록

```
curl -X DELETE http://localhost:8083/connectors/inventory-connector

curl -X POST -H "Content-Type: application/json" --data '{
  "name": "inventory-connector",
  "config": {
    "connector.class": "io.debezium.connector.mysql.MySqlConnector",
    "tasks.max": "1",
    "database.hostname": "mysql",
    "database.port": "3306",
    "database.user": "debezium",
    "database.password": "dbz",
    "database.server.id": "184054",
    "database.server.name": "mysql",
    "database.whitelist": "inventory",
    "schema.history.internal.kafka.bootstrap.servers": "kafka:29092",
    "schema.history.internal.kafka.topic": "schema-changes.inventory",
    "topic.prefix": "mysql",
    "tombstones.on.delete": "true",
    "transforms": "unwrap",
    "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
    "transforms.unwrap.drop.tombstones": "false",
    "time.precision.mode": "connect",
    "database.connectionTimeZone": "Asia/Seoul"
  }
}' http://localhost:8083/connectors
```

# 카프카 JDBC Sink 커넥트

## 플러그인 설치

1. plugins/jdbc_connector 디렉토리 생성
2. https://www.confluent.io/hub/confluentinc/kafka-connect-jdbc > Self Hosted 다운
3. https://mvnrepository.com/artifact/mysql/mysql-connector-java > 버전 선택 > jar 다운
4. 압축 해제 및 plugins/jdbc_connector 에 jar 파일들 이동

## 커넥터 등록

```
curl -X POST -H "Content-Type: application/json" --data '
{
    "name": "mysql_jdbc_sink_customers_01",
    "config": {
        "connector.class": "io.confluent.connect.jdbc.JdbcSinkConnector",
        "tasks.max": "1",
        "topics": "mysql.inventory.customers",
		    "connection.url": "jdbc:mysql://mysql:3306/sink",
        "connection.user": "root",
        "connection.password": "debezium",
        "table.name.format": "customers",
        "insert.mode": "upsert",
        "pk.fields": "id",
        "pk.mode": "record_key",
        "delete.enabled": "true",
        "key.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}' http://localhost:8084/connectors
```
