# (C) Copyright 2020 Hewlett Packard Enterprise Development LP.
# Create a minimal image for all config containers.  Use ubuntu:18.04 plus wait-for and it's required tools wget and netcat
FROM ubuntu:18.04 as configbase
# Test env deps
RUN apt-get update && apt-get upgrade -y \
    && apt-get install -y wget netcat

# Build wait for tool
RUN cd /tmp/ && wget --no-check-certificate https://api.github.com/repos/eficode/wait-for/tarball/828386460d138e418c31a1ebf87d9a40f5cedc32 -O - | tar xzf -
RUN mv /tmp/eficode-wait-for-*/wait-for /tmp/

# Create config schemaregistry with needed tools from entrypoint.sh (which is a bash script)
# - bash
# - wait-for (netcat) and wait-for from configbase
# - python for script that pushes to schemaregistry. Plus os, sys and requests modules.
# - curl to get avro-cli
# - apt-utils to process deb conf
# - avro-cli to check the validity of avro schema files
# - Java 8 is required by avro-cli (java 11 causes issues with Springframework)
FROM ubuntu:18.04
RUN apt-get update && apt-get upgrade -y \
    && apt-get install -y bash wget netcat curl apt-utils jq
COPY --from=configbase /tmp/wait-for /tmp/wait-for

COPY entrypoint.sh *.json /tmp/

CMD ["/tmp/entrypoint.sh"]
