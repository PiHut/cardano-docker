FROM ubuntu:focal
LABEL name="cardano_monitor"
LABEL description="Monitoring tools for Cardano node and its host"
LABEL maintainer="https://github.com/pascallapointe"

WORKDIR /cardano

# Install base utilities and dependencies
RUN apt-get update
RUN apt-get install -y wget

ARG ARCHITECTURE

# Set architecture-specific variables
RUN if [ "$ARCHITECTURE" = "x86_64" ]; then \
    ARCH=amd64 \
    ; elif [ "$ARCHITECTURE" = "aarch64" ]; then \
    ARCH=arm64 \
    ; fi

# Define version numbers for different architectures
ARG PROM_VER=2.45.3
ARG NEXP_VER=1.7.0

# Define arguments/variables
ARG PROM_DIR="prometheus"
ARG PROM_FILE="prometheus-${PROM_VER}.linux-${ARCH}.tar.gz"
ARG PROM_URL="https://github.com/prometheus/prometheus/releases/download/v${PROM_VER}/${PROM_FILE}"

ARG NEXP_DIR="node_exporter"
ARG NEXP_FILE="node_exporter-${NEXP_VER}.linux-${ARCH}.tar.gz"
ARG NEXP_URL="https://github.com/prometheus/node_exporter/releases/download/v${NEXP_VER}/${NEXP_FILE}"

# Download all libraries
RUN wget ${PROM_URL}
RUN wget ${NEXP_URL}

# Extract files
RUN mkdir ${PROM_DIR}
RUN tar zxC ${PROM_DIR} -f ${PROM_FILE} --strip-components 1

RUN mkdir ${NEXP_DIR}
RUN tar zxC ${NEXP_DIR} -f ${NEXP_FILE} --strip-components 1

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
RUN rm ${PROM_FILE}
RUN rm ${NEXP_FILE}
RUN apt-get purge -y wget

RUN apt-get autoremove -y
RUN apt-get clean
RUN apt-get autoclean

#USER cardano:cardano

CMD ["start-monitoring.sh"]
