FROM ubuntu:focal
LABEL name="cardano_monitor"
LABEL description="Monitoring tools for Cardano node and its host"
LABEL maintainer="https://github.com/pascallapointe"

WORKDIR /cardano

# Install base utilities and dependencies
RUN apt-get update
RUN apt-get install -y wget

# Define arguments/variables
ARG ARCHITECTURE

ARG PROM_VER=2.45.3
ARG PROM_DIR="prometheus"
ARG x86_PROM_FILE="prometheus-${PROM_VER}.linux-amd64.tar.gz"
ARG ARM_PROM_FILE="prometheus-${PROM_VER}.linux-amd64.tar.gz"

# Variables for Node Exporter
ARG NEXP_VER=1.7.0
ARG NEXP_DIR="node_exporter"
ARG x86_NEXP_FILE="node_exporter-${NEXP_VER}.linux-amd64.tar.gz"
ARG ARM_NEXP_FILE="node_exporter-${NEXP_VER}.linux-arm64.tar.gz"

# Download all libraries
RUN if [ "$ARCHITECTURE" = "x86_64" ]; then \
    wget "https://github.com/prometheus/prometheus/releases/download/v${PROM_VER}/${x86_PROM_FILE}" ;\
    wget "https://github.com/prometheus/node_exporter/releases/download/v${NEXP_VER}/${x86_NEXP_FILE}" ;\
    else \
    wget "https://github.com/prometheus/prometheus/releases/download/v${PROM_VER}/${ARM_PROM_FILE}" ;\
    wget "https://github.com/prometheus/node_exporter/releases/download/v${NEXP_VER}/${ARM_NEXP_FILE}" ;\
    fi

# Extract files
RUN if [ "$ARCHITECTURE" = "x86_64" ]; then \
    mkdir ${PROM_DIR} ;\
    tar zxC ${PROM_DIR} -f ${x86_PROM_FILE} --strip-components 1 ;\
    mkdir ${NEXP_DIR} ;\
    tar zxC ${NEXP_DIR} -f ${x86_NEXP_FILE} --strip-components 1 ;\
    else \
    mkdir ${PROM_DIR} ;\
    tar zxC ${PROM_DIR} -f ${ARM_PROM_FILE} --strip-components 1 ;\
    mkdir ${NEXP_DIR} ;\
    tar zxC ${NEXP_DIR} -f ${ARM_NEXP_FILE} --strip-components 1 ;\
    fi

# Install start script
COPY files/start-monitoring.sh /cardano/scripts/start-monitoring.sh
RUN chmod 544 /cardano/scripts/start-monitoring.sh

# Add Prometheus configuration, can be overridden using a 'bind mount' volume
COPY files/prometheus.yml /cardano/config/prometheus.yml

# Creating non root user cardano
#RUN useradd -m cardano

# Add data store folder
RUN mkdir /cardano/data

# Add permissions
#RUN chown -R cardano:cardano /cardano
RUN chmod g+s /cardano

# Export executable path
ENV PATH=/cardano/${PROM_DIR}:/cardano/${NEXP_DIR}:/cardano/scripts:$PATH

# Image clean-up
RUN if [ "$ARCHITECTURE" = "x86_64" ]; then \
    rm ${x86_PROM_FILE} ;\
    rm ${x86_NEXP_FILE} ;\
    else \
    rm ${arm_PROM_FILE} ;\
    rm ${arm_NEXP_FILE} ;\
    fi

RUN apt-get purge -y wget
RUN apt-get autoremove -y
RUN apt-get clean
RUN apt-get autoclean

#USER cardano:cardano

CMD ["start-monitoring.sh"]
