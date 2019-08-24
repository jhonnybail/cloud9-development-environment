# Replace cloud9/ws-php with any existing Ubuntu-based Docker image ID you want.
FROM ubuntu:xenial

# Use default answers for all setup prompts.
ENV DEBIAN_FRONTEND noninteractive

# Add Ruby Version Manager (rvm) to PATH.
ENV PATH="/usr/local/rvm/bin/:$PATH"

# Set ownership defaults.
RUN rm -rf /var/c9sdk && \
    mkdir /var/c9sdk

RUN apt-get update
RUN apt-get install openssl wget curl -y
RUN apt-get install git vim nano tmux locales -y
RUN apt-get remove nodejs npm -y

ENV DOCKERIZE_VERSION v0.6.1
RUN wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && rm dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz

# Install Node.js.
RUN curl -sL https://deb.nodesource.com/setup_11.x | bash -
RUN apt-get install nodejs python2.7 make gcc -y

# Populate environment variables that are required by Cloud9 Core.
RUN echo "export USER=\$USER\n\
export C9_PROJECT=\$PROJECT_NAME\n\
export C9_USER=\$USER\n\
export C9_HOSTNAME=\$PROJECT_HOSTNAME\n\
export C9_PORT=\$PROJECT_PORT\n\
export IDE_OFFLINE=1\n\
alias c9=/var/c9sdk/bin/c9" > /etc/profile.d/offline.sh

RUN sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    update-locale LANG=en_US.UTF-8

ENV LANG en_US.UTF-8

# Download Cloud9 Core into the Docker container.
WORKDIR /var
RUN git clone https://github.com/c9/core.git c9sdk

# Install Cloud9 Core within the Docker container.
WORKDIR c9sdk
RUN scripts/install-sdk.sh

# Set additional ownership defaults.
#RUN chown -R ubuntu:ubuntu /home/ubuntu/workspace

COPY ["./Remote XDebug.run", "/var/c9sdk/plugins/c9.ide.run/runners/"]

# Have the Docker container listen to these ports at run time.
EXPOSE 80 5050 8080 8081 8082 9000

# Run Cloud9 Core within the Docker container.
ENTRYPOINT ["dockerize", \
    "-template", \
    "/var/c9sdk/plugins/c9.ide.run/runners/Remote XDebug.run:/var/c9sdk/plugins/c9.ide.run/runners/Remote XDebug.run", \
    "node", \
    "server.js", \
    "-w", \
    "/var/www/html", \
    "--auth", \
    ":", \
    "--listen", \
    "0.0.0.0", \
    "--port", \
    "5050"]
