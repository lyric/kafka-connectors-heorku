# Kafka Connect + JDBC Connector + Heroku Data

## Getting Started

### Prerequisites

**Addons**
* Heroku Postgres Addon
* Heroku Kafka Addon

**Kafka Topics**
For each Postgres Table that is being sync'd, you will need to create an associated
Kafka topic using the naming scheme `pg-<table_name>`.

**Kafka Consumer Group**
You will also need to create the Consumer Group `kafka-connect`

### Config Vars

* `KAFKA_ADDON`
* `POSTGRES_ADDON`
* `TRUSTSTORE_PASSWORD`
* `KEYSTORE_PASSWORD`

### Buildpacks

* Apt: `https://github.com/heroku/heroku-buildpack-apt` < TODO fails on keys https://github.com/heroku/heroku-buildpack-apt/pull/41
* Apt: `heroku buildpacks:add --index=1 https://github.com/amiel/heroku-buildpack-apt#feature/support-adding-keys
`
* JVM: `heroku/jvm`

### Setup
## Using the app.json

heroku kafka:topics:create pg-csa_cta

## Manually
heroku config:set TRUSTSTORE_PASSWORD="trustypassword9"
heroku config:set KEYSTORE_PASSWORD="keytrustypassword9"
heroku buildpacks:add --index 1 heroku-community/apt
heroku buildpacks:add heroku/jvm

### Verify install
heroku ps:exec 
https://devcenter.heroku.com/articles/exec#getting-started
https://[appname].herokuapp.com/connectors


Check the log for something like "INFO Added plugin 'io.confluent.connect.jdbc.JdbcSourceConnector'"

### JDBC connector info
https://www.confluent.io/blog/kafka-connect-deep-dive-jdbc-source-connector
https://docs.confluent.io/current/connect/managing/connectors.html#connect-bundled-connectors
https://docs.confluent.io/current/connect/kafka-connect-jdbc/source-connector/index.html
https://docs.confluent.io/current/connect/kafka-connect-jdbc/source-connector/source_config_options.html

configure the connector

curl -X POST -H "Content-Type: application/json" -d @jdbc-postgres-example.json "http://lyric-connect-jdbc.herokuapp.com/connectors"


curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" -d '{
        "name": "jdbc_source_postgres_01",
        "config": {
                "connector.class": "io.confluent.connect.jdbc.JdbcSourceConnector",
                  "connection.url": "jdbc:postgresql://postgres:5432/postgres",
                "connection.user": "connect_user",
                "connection.password": "asgard",
                "topic.prefix": "postgres-01-",
                "mode":"bulk",
                "poll.interval.ms" : 3600000
                }
        }'


curl -X POST -H "Content-Type: application/json" -d @jdbc-source-with-schema.json "http://localhost:8083/connectors"
...
{"name":"jdbc-postgres-source-with-schema-connector","config":{"connector.class":"io.confluent.connect.jdbc.JdbcSourceConnector","tasks.max":"1","mode":"bulk","connection.url":"jdbc:postgresql://localhost:5432/customers?user=postgres&password=newpass","table.whitelist":"addresses","topic.prefix":"jdbc-postgresql-with-schema-example-","name":"jdbc-postgres-source-with-schema-connector"},"tasks":[],"type":null}