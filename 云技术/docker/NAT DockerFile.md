# NAT Docker File

* Backend

```dockerfile
rm -rf docker/$PROJECT_NAME
mkdir -p docker/$PROJECT_NAME
cp -r target/$PROJECT_NAME-1.0.0.jar docker/$PROJECT_NAME/
cp -r certificate docker/$PROJECT_NAME/

cd docker

# 创建 Dockerfile
cat <<EOF > Dockerfile
FROM harbor.newegg.org/base/openjdk:8-jdk-alpine
RUN apk add --no-cache tini
ENTRYPOINT ["/sbin/tini", "--"]
VOLUME ["/usr/bin/mvn"]
MAINTAINER DAE
COPY $PROJECT_NAME /opt/$PROJECT_NAME
WORKDIR /opt/$PROJECT_NAME
RUN ls /usr/lib/jvm/java-1.8-openjdk/ | echo
RUN ls /opt/$PROJECT_NAME 
Run cd /usr/lib/jvm/java-1.8-openjdk/jre/lib/security/ && echo yes | keytool -import -alias NeweggWebGateway -storepass changeit -keystore cacerts -file /opt/$PROJECT_NAME/certificate/PROXY.cer

EXPOSE 8080

CMD ["java", "-jar", "$PROJECT_NAME-1.0.0.jar", \
  "--spring.profiles.active=gdev", \
  "--server.port=8080", \
  "--springfox.documentation.swagger.v2.host=gdev-services.newegg.space/$DISCOVER_ID", \
  "--jasypt.encryptor.password=NAT@GDEV"]

EOF
```

* Console

```dockerfile
deploy_env=gdev

FROM harbor.newegg.org/base/nginx:stable-alpine

LABEL authors="Kent <kent.l.tan@newegg.com>"
MAINTAINER DAE

RUN mkdir /ng-alain
WORKDIR /ng-alain

# Just copy the dist directory and nginx config file 
COPY ./dist ./dist
COPY _nginx/${deploy_env}.conf /etc/nginx/conf.d/nginx.conf

RUN rm -rf /usr/share/nginx/html/*

COPY /dist /usr/share/nginx/html

CMD [ "nginx", "-g", "daemon off;"]
```

```dockerfile
deploy_env=prod

echo -e ">> $(date --rfc-3339=s) begin build app"
./node_modules/@angular/cli/bin/ng build --prod
node ./_rels/replace-html.js prd
echo -e ">> $(date --rfc-3339=s) end build app \n"

rm -f run.sh
touch run.sh
chmod 777 run.sh 
echo '#!/bin/bash' >> run.sh
echo 'nginx -c /etc/nginx/nginx.conf' >> run.sh
echo 'nginx -s reload' >> run.sh
echo '/usr/bin/nginx-prometheus-exporter -nginx.scrape-uri http://127.0.0.1:8080/stub_status' >> run.sh
echo 'tail -f /dev/null' >> run.sh

# generate dockerfile
echo -e ">> $(date --rfc-3339=s) begin generate dockerfile"

FROM dev-harbor.newegg.org/sao/nginx-pe:0.0.1
USER root

LABEL authors="platform engineering <nesc-cd.mis.platformengineering@newegg.com>"
MAINTAINER DAE
COPY _nginx/${deploy_env}.conf /etc/nginx/conf.d/default.conf
COPY /dist /usr/share/nginx/html
COPY run.sh /usr/bin/run.sh

RUN chown nginx:nginx -R /etc/nginx/conf.d
RUN chown nginx:nginx -R /usr/share/nginx/html
RUN chown nginx:nginx -R /usr/bin/nginx-prometheus-exporter
RUN chown nginx:nginx /usr/bin/run.sh

USER nginx
EXPOSE 8080 9113
CMD ["/bin/sh", "/usr/bin/run.sh"]
```

