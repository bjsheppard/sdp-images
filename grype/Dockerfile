ARG BASE_REGISTRY=registry.access.redhat.com
ARG BASE_IMAGE=ubi8/ubi
ARG BASE_TAG=8.4
FROM ${BASE_REGISTRY}/${BASE_IMAGE}:${BASE_TAG}

### Required Atomic/OpenShift Labels - https://github.com/projectatomic/ContainerApplicationGenericLabels
LABEL name="Solutions Delivery Platform: Jenkins Agent" \
      maintainer="terrana_steven@bah.com" \
      vendor="Booz Allen Hamilton" \
      version="0.28.0" \
      release="0.28.0" \
      summary="Anchore Grype container" \
      description="The Anchore Grype container image for the Solutions Delivery Platform"

### add licenses to this directory
COPY LICENSE /licenses

### Add necessary Red Hat repos and packages here
RUN INSTALL_PKGS="java-1.8.0-openjdk-devel ruby glibc glibc-common" && \
    yum -y update-minimal --setopt=tsflags=nodocs --security  && \
    yum -y install --setopt=tsflags=nodocs ${INSTALL_PKGS}

ENV GRYPE_VERSION 0.28.0
ENV HOME /root
ENV JAVA_HOME /usr/lib/jvm/java
ENV GRYPE_DESTINATION_DIR /usr/local/bin

ARG user=grype

# Install Grype
RUN curl -sSfL https://raw.githubusercontent.com/anchore/grype/main/install.sh | sh -s -- -b <GRYPE_DESTINATION_DIR> <GRYPE_VERSION>

# Add user, create required directories  and cleanup
RUN useradd -ms /bin/bash ${user}                                           && \
    chown -R ${user}:${user} /usr/local/bin/grype                           && \
    mkdir /report                                                           && \
    chown -R ${user}:${user} /report                                        && \
    yum --nogpgcheck --disableplugin=subscription-manager clean all

### Insert Container Entry Point Script
COPY container-entrypoint.sh /usr/local/bin/container-entrypoint.sh

### Set script as executable
RUN chmod +x /usr/local/bin/container-entrypoint.sh

USER ${user}

VOLUME ["/src" "/report"]
WORKDIR /src

CMD []
ENTRYPOINT [ "/bin/sh", "/usr/local/bin/container-entrypoint.sh" ]
