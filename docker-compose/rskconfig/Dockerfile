FROM ubuntu:latest

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y --no-install-recommends curl git openjdk-8-jdk openjdk-8-jre

RUN groupadd --gid 888 rsk && useradd rsk -d /var/lib/rsk -s /sbin/nologin --uid=888 --gid=888 && \
    mkdir /var/lib/rsk && \
    mkdir /home/rskj && \
    chown -R rsk:rsk /home/rskj && \
    chown -R rsk:rsk /var/lib/rsk

WORKDIR /home/rskj

USER rsk

RUN git clone https://github.com/rsksmart/rskj.git .

COPY ./rsk-dev.json rskj-core/src/main/resources/genesis/

RUN  ./configure.sh && \
     ./gradlew build -x test

RUN mkdir /home/rskj/logs && \
    mkfifo /home/rskj/logs/rsk.log && \
    ln -sf /proc/1/fd/1 /home/rskj/logs/rsk.log

COPY ./logback.xml .
COPY ./regtest.conf .

ENTRYPOINT ["java"]
CMD ["-Xss4M", "-Dlogback.configurationFile='/home/rskj/logback.xml'", "-Drsk.conf.file=/home/rskj/regtest.conf", "-Drpc.skipRemasc=true", "-cp", "/home/rskj/rskj-core/build/libs/rskj-core-3.1.0-SNAPSHOT-all.jar", "co.rsk.Start"]