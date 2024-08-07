# (C) Copyright 2020 Hewlett Packard Enterprise Development LP.
#
# Licensed under the Apache License, Version 2.0 (the "License"); you may
# not use this file except in compliance with the License. You may obtain
# a copy of the License at
#       
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
# WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
# License for the specific language governing permissions and limitations
# under the License.

FROM openjdk:8-jdk AS druid

# requirement for wait-for and druid (which needs python2)
RUN apt update && apt install -y netcat python3-pip python2

RUN wget https://archive.apache.org/dist/druid/0.17.1/apache-druid-0.17.1-bin.tar.gz \
    && tar -xzf apache-druid-0.17.1-bin.tar.gz \
    && mv apache-druid-0.17.1 /opt/druid/

# Swap hdfs for avro as we are only using local storage in all the common properties. These are all the same in this version but are not links
RUN sed -i 's/druid-hdfs-storage/druid-avro-extensions/' /opt/druid/conf/druid/single-server/nano-quickstart/_common/common.runtime.properties
RUN sed -i 's/druid-hdfs-storage/druid-avro-extensions/' /opt/druid/conf/druid/single-server/micro-quickstart/_common/common.runtime.properties
RUN sed -i 's/druid-hdfs-storage/druid-avro-extensions/' /opt/druid/conf/druid/single-server/small/_common/common.runtime.properties
RUN sed -i 's/druid-hdfs-storage/druid-avro-extensions/' /opt/druid/conf/druid/single-server/medium/_common/common.runtime.properties
RUN sed -i 's/druid-hdfs-storage/druid-avro-extensions/' /opt/druid/conf/druid/single-server/large/_common/common.runtime.properties
RUN sed -i 's/druid-hdfs-storage/druid-avro-extensions/' /opt/druid/conf/druid/single-server/xlarge/_common/common.runtime.properties

RUN cd /tmp/ && wget --no-check-certificate https://api.github.com/repos/eficode/wait-for/tarball/828386460d138e418c31a1ebf87d9a40f5cedc32 -O - | tar xzf -

RUN mv /tmp/eficode-wait-for-*/wait-for /tmp/

# The applications bundled with druid assume that you have a python in your PATH, but no such file exists out of the box
RUN update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.9 1
# Install druid dependencies
#COPY ./requirements.txt .
#RUN python -m pip install -r requirements.txt

COPY entrypoint.sh /tmp/

WORKDIR /opt/druid/
ENTRYPOINT /tmp/entrypoint.sh
