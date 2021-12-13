FROM registry.access.redhat.com/ubi8

ENV USER_UID=1001 \
    USER_NAME=splunk

LABEL name="kafka-connect-splunk" \
      maintainer="support@splunk.com" \
      vendor="splunk" \
      version="2.0.4" \
      release="1" \
      summary="Splunk Connect for Kafka" \
      description="Splunk Connect for Kafka is a Kafka Connect Sink for Splunk."

# install kafka connnect binaries
COPY /splunk-kafka-connect /splunk-kafka-connect

# Install rsyslog and java-11 packages
RUN dnf install -y rsyslog procps-ng java-11-openjdk-headless && \
    dnf clean all && \
    rm -rf /var/cache/dnf

RUN mkdir /licenses \
    && curl -s -o /licenses/apache-2.0.txt https://www.apache.org/licenses/LICENSE-2.0.txt \
    && curl -s -o /licenses/EULA_Red_Hat_Universal_Base_Image_English_20190422.pdf https://www.redhat.com/licenses/EULA_Red_Hat_Universal_Base_Image_English_20190422.pdf

ENTRYPOINT ["/splunk-kafka-connect/bin/connect-distributed.sh", "/splunk-kafka-connect/config/connect-distributed.properties"]

USER ${USER_UID}
