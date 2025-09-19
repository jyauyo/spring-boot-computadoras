FROM docker:28.1.1

# Instala dependencias necesarias
RUN apk add --no-cache openjdk21 maven curl tar

# Instala Maven 3.9.x (usa la última 3.9.x disponible)
ENV MAVEN_VERSION=3.9.9
RUN curl -fsSL https://archive.apache.org/dist/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz \
    | tar -xz -C /opt

# Instala ArgoCD CLI (última versión estable)
ENV ARGOCD_VERSION=2.11.3
RUN curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/v${ARGOCD_VERSION}/argocd-linux-amd64 && \
    chmod +x /usr/local/bin/argocd


# Crear usuario jenkins con directorio home y permisos de sudo
RUN adduser -D -h /home/jenkins jenkins && \
    chown -R jenkins:jenkins /home/jenkins && \
    chown -R jenkins:jenkins /opt/apache-maven-${MAVEN_VERSION} && \
    chown -R jenkins:jenkins /tmp && \
    chmod 777 /tmp
#RUN useradd -m -u 1000 -s /bin/bash jenkins

USER jenkins
WORKDIR /home/jenkins

# Establece JAVA_HOME
ENV JAVA_HOME=/usr/lib/jvm/java-21-openjdk
ENV M2_HOME=/opt/apache-maven-${MAVEN_VERSION}
ENV PATH=$JAVA_HOME/bin:$M2_HOME/bin:$PATH

CMD ["sh"]

# En el computador en donde se ejecuta Jenkins ejecutar:
# sudo chmod 666 /var/run/docker.sock
