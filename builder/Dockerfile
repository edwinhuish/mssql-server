FROM ubuntu:18.04

ARG CONTAINER_PACKAGE_URL

ENV TZ=UTC \
    ACCEPT_EULA=Y \
    MSSQL_PID=Express \
    MSSQL_SA_PASSWORD=YourStrong!Passw0rd \
    MSSQL_DATA_DIR=/data \
    MSSQL_LOG_DIR=/data \
    MSSQL_MASTER_DATA_FILE=/var/opt/mssql/data/master.mdf \
    MSSQL_MASTER_LOG_FILE=/var/opt/mssql/data/mastlog.ldf \
    MSSQL_BACKUP_DIR=/backup \
    MSSQL_DUMP_DIR=/dump \
    MSSQL_ERROR_LOG_DIR=/log \
    MSSQL_ERROR_LOG_FILE=${MSSQL_ERROR_LOG_DIR}/err.log \
    DEBIAN_FRONTEND=noninteractive

VOLUME $MSSQL_DATA_DIR $MSSQL_BACKUP_DIR $MSSQL_ERROR_LOG_DIR $MSSQL_DUMP_DIR

COPY ./unlimited-memory.py /tmp/unlimited-memory.py

RUN if [ $CONTAINER_PACKAGE_URL ] ; then \
      sed -i "s/security.ubuntu.com/${CONTAINER_PACKAGE_URL}/g" /etc/apt/sources.list ; \
      sed -i "s/archive.ubuntu.com/${CONTAINER_PACKAGE_URL}/g" /etc/apt/sources.list ; \
    fi && \
    # install requirement
    apt-get update && apt-get install -yq sudo curl apt-transport-https gnupg tzdata && \
    # change time zone
    rm /etc/localtime && cp "/usr/share/zoneinfo/$TZ" /etc/localtime && echo "$TZ" > /etc/timezone && \
    # add source and update
    curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - && \
    curl https://packages.microsoft.com/config/ubuntu/18.04/mssql-server-2019.list | tee /etc/apt/sources.list.d/mssql-server.list && \
    apt-get update && \
    # install
    apt-get install -y mssql-server && \
    # remove 2G memory requirement
    chmod +x /tmp/unlimited-memory.py && \
    python /tmp/unlimited-memory.py && \
    chmod +x /opt/mssql/bin/sqlservr && \
    # clear data
    apt-get clean && \
    rm -rf /var/cache/apt/archives && \
    rm -rf /var/lib/apt/lists && \
    rm -rf /tmp/*

EXPOSE 1433

CMD /opt/mssql/bin/sqlservr