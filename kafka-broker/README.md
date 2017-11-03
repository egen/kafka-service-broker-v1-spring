# kafka-broker
A cloud foundry service broker for kafka.

## Prerequisites
The [simple-service-broker](https://github.com/cf-platform-eng/simple-service-broker) project is a dependency for the kafka broker. Please see the [README](https://github.com/cf-platform-eng/simple-service-broker/blob/master/simple-broker/README.md) for instructions on building this dependency.

## Using kafka-broker
1. kafka-broker requires a redis datastore, such as Dingo Redis. Any Redis service broker should do, as long as [Spring Cloud Cloud Foundry Connector can automatically discovery it](http://cloud.spring.io/spring-cloud-connectors/spring-cloud-cloud-foundry-connector.html#_redis). To set this up:

  ```bash
  cf create-service dingo-redis ready redis-for-kafka
  ```

2. The broker makes use of spring-security to protect itself against unauthorized meddling. To set its password edit the [application.properties file](https://github.com/cf-platform-eng/kafka-service-broker/blob/master/kafka-broker/src/main/resources/application.properties) (you probably don't want to check this in!)
3. Edit the [manifest.yml](https://github.com/cf-platform-eng/kafka-service-broker/blob/master/kafka-broker/manifest.yml) file as needed for your CF install.

4. Build the broker:

  ```bash
  git clone https://github.com/dingotiles/kafka-service-broker.git
  cd kafka-broker
  mvn clean install
  ```

5. Push the broker to cf:

  ```bash
  cf push
  ```

6. Register the broker:

  ```bash
  cf create-service-broker dingo-kafka user $SECURITY_USER_PASSWORD http://dingo-kafka-broker.10-58-111-49.sslip.io
  ```
7. See the broker:

  ```bash
  $ cf service-brokers
  Getting service brokers as admin...

  name                  url
  dingo-kafka-broker    http://dingo-kafka-broker.10-58-111-49.sslip.io

  $ cf service-access
  Getting service access as admin...
  ...
  broker: dingo-kafka
     service       plan    access   orgs
     dingo-kafka   topic   all

  $ cf enable-service-access dingo-kafka
  Enabling access to all plans of service KafkaService for all orgs as admin...

  $ cf marketplace
  Getting services from marketplace in org your-org / space your-space as you...
  OK

  service        plans   description
  dingo-kafka    topic   Apache Kafka
  ...
  ```
