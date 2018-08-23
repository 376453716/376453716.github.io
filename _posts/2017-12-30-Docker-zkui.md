### zkui

Dockerfile
```Dockerfile
FROM java:8

MAINTAINER Miguel Garcia Puyol <miguelpuyol@gmail.com>

WORKDIR /var/app

ADD zkui-*.jar /var/app/zkui.jar
ADD config.cfg /var/app/config.cfg
ADD bootstrap.sh /var/app/bootstrap.sh

ENTRYPOINT ["/var/app/bootstrap.sh"]

EXPOSE 9090
```

bootstrap
```
#!/bin/sh

#ZK_SERVER=${ZK_SERVER:-"localhost:2181"}

USER_SET=${USER_SET:-"{\"users\": [{ \"username\":\"admin\" , \"password\":\"admin123\",\"role\": \"ADMIN\" \},{ \"username\":\"appconfig\" , \"password\":\"appconfig\",\"role\": \"USER\" \}]\}"}
LOGIN_MESSAGE=${LOGIN_MESSAGE:-"Please login using admin/manager or appconfig/appconfig."}

sed -i "s/^zkServer=.*$/zkServer=$ZK_SERVER/" /var/app/config.cfg

sed -i "s/^userSet = .*$/userSet = $USER_SET/" /var/app/config.cfg
sed -i "s/^loginMessage=.*$/loginMessage=$LOGIN_MESSAGE/" /var/app/config.cfg

echo "Starting zkui with server $ZK_SERVER"

exec java -Xms50m -Xmx50m -XX:MetaspaceSize=30m -XX:MaxMetaspaceSize=30m -jar /var/app/zkui.jar
```

zkui compose
```
zkui:
    links:
      - zoo1
    hostname: zkui
    container_name: zkui
    image: zkui-local
    environment:
     - ZK_SERVER=zoo1:2181
    ports:
     - 9090:9090
```