FROM openjdk:8u171-jdk-alpine3.8

# Spark Verison
ENV SPARK_VERSION         2.4.3
ENV HADOOP_VERSION        2.9.2
ENV SPARK_HOME            /opt/spark
ENV SPARK_JARS            /opt/spark/jars
ENV HADOOP_HOME           /opt/hadoop
ENV SPARK_CHECKSUM_URL    https://www.apache.org/dist/spark
ENV SPARK_DOWNLOAD_URL    https://www-us.apache.org/dist/spark
ENV HADOOP_CHECKSUM_URL   https://www-us.apache.org/dist/hadoop
ENV HADOOP_DOWNLOAD_URL   https://www.apache.org/dist/hadoop

ENV GLIBC_VERSION         2.26-r0

# Setup basics
RUN set -ex && \
    apk upgrade --update && \
    apk add --update libstdc++ ca-certificates bash openblas curl findutils && \
    for pkg in glibc-${GLIBC_VERSION} glibc-bin-${GLIBC_VERSION} glibc-i18n-${GLIBC_VERSION}; do curl -sSL https://github.com/andyshinn/alpine-pkg-glibc/releases/download/${GLIBC_VERSION}/${pkg}.apk -o /tmp/${pkg}.apk; done && \
    apk add --allow-untrusted /tmp/*.apk && \
    rm -v /tmp/*.apk && \
    ( /usr/glibc-compat/bin/localedef --force --inputfile POSIX --charmap UTF-8 C.UTF-8 || true ) && \
    echo "export LANG=C.UTF-8" > /etc/profile.d/locale.sh && \
    /usr/glibc-compat/sbin/ldconfig /lib /usr/glibc-compat/lib

# spark
RUN wget -O spark.sha ${SPARK_CHECKSUM_URL}/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-without-hadoop.tgz.sha512
#RUN wget -O spark.sha https://www.apache.org/dist/spark/spark-2.4.0/spark-2.4.0-bin-without-hadoop.tgz.sha512
RUN wget -O spark.tar.gz ${SPARK_DOWNLOAD_URL}/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-without-hadoop.tgz

RUN mkdir -p ${SPARK_HOME} && \
    export SPARK_SHA512_SUM=$(grep -o "[A-F0-9]\{8\}" spark.sha | awk '{print}' ORS='' | tr '[:upper:]' '[:lower:]') && \
    rm -f spark.sha && \
    echo "${SPARK_SHA512_SUM}  spark.tar.gz" | sha512sum -c - && \
    gunzip -c spark.tar.gz | tar -xf - -C $SPARK_HOME --strip-components=1 && \
    rm -f spark.tar.gz

# get hadoop
#RUN wget -O hadoop.mds ${HADOOP_CHECKSUM_URL}/common/hadoop-${HADOOP_VERSION}/hadoop-${HADOOP_VERSION}.tar.gz.mds
#RUN wget -O hadoop.tar.gz ${HADOOP_DOWNLOAD_URL}/common/hadoop-${HADOOP_VERSION}/hadoop-${HADOOP_VERSION}.tar.gz
RUN wget -O hadoop.mds ${HADOOP_CHECKSUM_URL}/core/hadoop-${HADOOP_VERSION}/hadoop-${HADOOP_VERSION}.tar.gz.mds
RUN wget -O hadoop.tar.gz ${HADOOP_DOWNLOAD_URL}/core/hadoop-${HADOOP_VERSION}/hadoop-${HADOOP_VERSION}.tar.gz

# install hadoop
RUN export HADOOP_SHA256_SUM=$(cat hadoop.mds | grep SHA256 | cut -d'=' -f2 | tr -d ' ' | tr '[:upper:]' '[:lower:]') && \
    echo "${HADOOP_SHA256_SUM}  hadoop.tar.gz" | sha256sum -c - && \
    mkdir -p ${HADOOP_HOME} && \
    gunzip -c hadoop.tar.gz | tar -xf - -C $HADOOP_HOME --strip-components=1

#Copy hadoop jars
RUN cp ${HADOOP_HOME}/share/hadoop/common/lib/*.jar ${SPARK_JARS} && \
    cp ${HADOOP_HOME}/share/hadoop/mapreduce/*.jar ${SPARK_JARS} && \
    cp ${HADOOP_HOME}/share/hadoop/yarn/*.jar ${SPARK_JARS} && \
    cp ${HADOOP_HOME}/share/hadoop/common/*.jar ${SPARK_JARS} && \
    rm ${SPARK_JARS}/commons-lang3-3.4.jar && rm ${SPARK_JARS}/commons-codec-1.4.jar && \
    rm ${SPARK_JARS}/commons-net-3.1.jar


# spark extensions
RUN wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/com/databricks/spark-xml_2.11/0.4.1/spark-xml_2.11-0.4.1.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/apache/spark/spark-avro_2.11/2.4.0/spark-avro_2.11-2.4.0.jar && \
    # aws hadoop
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/apache/hadoop/hadoop-aws/${HADOOP_VERSION}/hadoop-aws-${HADOOP_VERSION}.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/com/amazonaws/aws-java-sdk/1.11.197/aws-java-sdk-1.11.197.jar && \
    # azure hadoop
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/apache/hadoop/hadoop-azure/${HADOOP_VERSION}/hadoop-azure-${HADOOP_VERSION}.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/com/microsoft/azure/azure-storage/7.0.0/azure-storage-7.0.0.jar && \
    # azure datalake store
    wget -P ${SPARK_JARS} http://repo.hortonworks.com/content/repositories/releases/org/apache/hadoop/hadoop-azure-datalake/2.7.3.2.6.5.3000-28/hadoop-azure-datalake-2.7.3.2.6.5.3000-28.jar && \
    wget -P ${SPARK_JARS} http://central.maven.org/maven2/com/microsoft/azure/azure-data-lake-store-sdk/2.3.1/azure-data-lake-store-sdk-2.3.1.jar && \
    # kafka
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/apache/kafka/kafka_2.11/1.1.0/kafka_2.11-1.1.0.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/apache/kafka/kafka-clients/1.1.0/kafka-clients-1.1.0.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/apache/spark/spark-sql-kafka-0-10_2.11/2.3.1/spark-sql-kafka-0-10_2.11-2.3.1.jar && \
    # azure eventhub
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/com/microsoft/azure/azure-eventhubs/1.2.0/azure-eventhubs-1.2.0.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/apache/qpid/proton-j/0.29.0/proton-j-0.29.0.jar && \
    # databases
    wget -P ${SPARK_JARS} https://repository.mulesoft.org/nexus/content/repositories/public/com/amazon/redshift/redshift-jdbc4/1.2.10.1009/redshift-jdbc4-1.2.10.1009.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/com/microsoft/sqlserver/mssql-jdbc/6.4.0.jre8/mssql-jdbc-6.4.0.jre8.jar && \
    wget -P ${SPARK_JARS} https://jdbc.postgresql.org/download/postgresql-42.2.2.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/com/datastax/spark/spark-cassandra-connector_2.11/2.0.5/spark-cassandra-connector_2.11-2.0.5.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/mysql/mysql-connector-java/5.1.45/mysql-connector-java-5.1.45.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/com/facebook/presto/presto-jdbc/0.209/presto-jdbc-0.209.jar && \
    # BigQueryJDBC uses difficult distribution mechanism
    wget -P /tmp https://storage.googleapis.com/simba-bq-release/jdbc/SimbaJDBCDriverforGoogleBigQuery42_1.1.6.1006.zip && \
    unzip -d ${SPARK_JARS} /tmp/SimbaJDBCDriverforGoogleBigQuery42_1.1.6.1006.zip *.jar && \
    rm ${SPARK_JARS}/jackson-core-2.1.3.jar && \
    rm /tmp/SimbaJDBCDriverforGoogleBigQuery42_1.1.6.1006.zip && \
    # logging
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/com/microsoft/azure/applicationinsights-core/1.0.9/applicationinsights-core-1.0.9.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/com/microsoft/azure/applicationinsights-logging-log4j1_2/1.0.9/applicationinsights-logging-log4j1_2-1.0.9.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/com/github/ptv-logistics/log4jala/1.0.4/log4jala-1.0.4.jar && \
    #geospark
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/datasyslab/geospark/1.1.3/geospark-1.1.3.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/datasyslab/geospark-sql_2.3/1.1.3/geospark-sql_2.3-1.1.3.jar && \
    # google cloud
    wget -P ${SPARK_JARS} http://central.maven.org/maven2/com/google/cloud/bigdataoss/gcs-connector/hadoop3-1.9.5/gcs-connector-hadoop3-1.9.5.jar && \
    # add back in hive jars missing because custom hadoop
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/spark-project/hive/hive-metastore/1.2.1.spark2/hive-metastore-1.2.1.spark2.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/spark-project/hive/hive-common/1.2.1.spark2/hive-common-1.2.1.spark2.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/spark-project/hive/hive-cli/1.2.1.spark2/hive-cli-1.2.1.spark2.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/spark-project/hive/hive-exec/1.2.1.spark2/hive-exec-1.2.1.spark2.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/spark-project/hive/hive-service/1.2.1.spark2/hive-service-1.2.1.spark2.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/spark-project/hive/hive-jdbc/1.2.1.spark2/hive-jdbc-1.2.1.spark2.jar && \
    wget -P ${SPARK_JARS} https://repo.maven.apache.org/maven2/org/apache/thrift/libthrift/0.9.2/libthrift-0.9.2.jar

# configure spark to find hadoop
RUN echo "SPARK_DIST_CLASSPATH=\$(/opt/hadoop/bin/hadoop classpath)" > /opt/spark/conf/spark-env.sh


# copy in etl library
COPY hello_2.11-1.0.jar /opt/spark/jars/hello.jar

WORKDIR $SPARK_HOME
# EOF


