# (C) Copyright 2020 Hewlett Packard Enterprise Development LP.

FROM ubuntu:18.04

RUN echo "test-tools"

# Test env deps
RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y bash strace tcpdump wget curl bind9-host

# Build deps
RUN apt-get install -y gcc g++ make cmake autoconf libtool wget libavro-dev libcurl4-gnutls-dev git gdb valgrind cppcheck zlib1g-dev libssl-dev libsasl2-dev libzstd-dev

# Jansson json library
RUN wget --no-check-certificate https://github.com/akheron/jansson/archive/v2.12.tar.gz -O - | tar xzf - 
RUN mv /jansson-2.12 /jansson && cd /jansson && autoreconf -fi ; ./configure && make && make install

# Yajl json library
#RUN cd / && wget --no-check-certificate https://github.com/lloyd/yajl/archive/2.1.0.tar.gz -O - | tar xzf - \
RUN cd / && wget --no-check-certificate https://github.com/lloyd/yajl/archive/refs/tags/2.1.0.tar.gz -O - | tar xzf - \
    && mv /yajl-2.1.0 /yajl && cd /yajl && ./configure && make && make install

# Build wait for tool
RUN cd /tmp/ && wget --no-check-certificate https://api.github.com/repos/eficode/wait-for/tarball/828386460d138e418c31a1ebf87d9a40f5cedc32 -O - | tar xzf -
RUN mv /tmp/eficode-wait-for-*/wait-for /tmp/

# Add other tools not needed for build
RUN apt-get install -y nmap netcat iputils-ping jq redis-server openssl gnupg software-properties-common openjdk-11-jre-headless vim emacs less bc iperf

# Confluent kafka tools
RUN wget -qO - https://packages.confluent.io/deb/5.4/archive.key | apt-key add -
RUN add-apt-repository "deb [arch=amd64] https://packages.confluent.io/deb/5.4 stable main"
RUN apt-get update && apt-get install -y confluent-community-2.12 librdkafka-dev kafkacat

# Add avrocat to work at commandline with avro files
RUN apt-get install -y python3 python3-pip git libavro-dev
RUN pip3 install colorama
#RUN cd /usr/src && git clone https://github.com/simonwahlgren/avrocat.git && cd avrocat && pip3 install -e .
# My fork of the repository so that I can control the versions in the requirements.txt
RUN cd /usr/src && git clone https://github.com/j-r-jones/avrocat.git && cd avrocat && python3 -m pip install -r requirements.txt &&  pip3 install -e .

# Bring in the environment of the simulator so we can play with it
COPY ./requirements.txt .
RUN python3 -m pip install -r requirements.txt

# mosquitto ppa used because in 18.04 the version is old
RUN apt-add-repository ppa:mosquitto-dev/mosquitto-ppa
RUN apt-get update && apt-get install -y mosquitto-clients

# mqtt-bench which is a go application, https://www.npmjs.com/package/mqtt-benchmark

RUN apt-get install -y golang unzip
RUN wget https://github.com/takanorig/mqtt-bench/releases/download/v0.3.0/mqtt-bench_linux_amd64-0.3.0.zip
RUN unzip mqtt-bench_linux_amd64-0.3.0.zip
RUN mv linux_amd64/mqtt-bench /tmp/

# mqtt-benchmark is a node js tool, https://www.npmjs.com/package/mqtt-benchmark
RUN apt-get install -y npm
RUN npm install mqtt-benchmark -g

COPY ./druid_query.json ./druid_query2.json ./query_druid.sh ./zookeeper-ready.sh ./km-scaling-test-rate.sh ./check-pipeline.sh ./client-plain.conf ./scaling-test.sh ./scaling-test-setup.sh ./print-schemaregistry.sh ./mqtt-bench.sh ./mqtt.batch /tmp/

ENV LD_LIBRARY_PATH /usr/local/lib

CMD sleep infinity
