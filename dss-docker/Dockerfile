FROM centos:7

ARG dssVersion

ENV DSS_VERSION="$dssVersion" \
    DSS_DATADIR="/home/dataiku/dss" \
    DSS_PORT=10000 \
    DSS_SPARK_VERSION="3.0.1"

# Dataiku account and data dir setup
RUN useradd dataiku \
    && mkdir -p /home/dataiku ${DSS_DATADIR} \
    && chown -Rh dataiku:dataiku /home/dataiku ${DSS_DATADIR}

# System dependencies
RUN yum install -y \
        epel-release \
    && yum install -y \
        file \
        acl \
        expat \
        git \
        nginx \
        unzip \
        zip \
        java-1.8.0-openjdk \
        python3 \
        freetype \
        libgfortran \
        libgomp \
        R-core-devel \
        libicu-devel \
        libcurl-devel \
        openssl-devel \
        libxml2-devel \
        python-devel \
        python3-devel \
    && yum clean all

# Download and extract DSS kit
RUN DSSKIT="dataiku-dss-$DSS_VERSION" \
    && cd /home/dataiku \
    && echo "+ Downloading kit" \
    && curl -OsS "https://cdn.downloads.dataiku.com/public/studio/$DSS_VERSION/$DSSKIT.tar.gz" \
    && echo "+ Extracting kit" \
    && tar xf "$DSSKIT.tar.gz" \
    && rm "$DSSKIT.tar.gz" \
    && "$DSSKIT"/scripts/install/installdir-postinstall.sh "$DSSKIT" \
    && chown -Rh dataiku:dataiku "$DSSKIT"

# Install required R packages
RUN mkdir -p /usr/local/lib/R/site-library \
    && R --slave --no-restore \
        -e "install.packages( \
            c('httr', 'RJSONIO', 'dplyr', 'curl', 'IRkernel', 'sparklyr', 'ggplot2', 'gtools', 'tidyr', \
            'rmarkdown', 'base64enc', 'filelock'), \
            '/usr/local/lib/R/site-library', \
            repos='https://cloud.r-project.org')"
# Download hadoop (WG)
RUN DSSHADOOPKIT="dataiku-dss-hadoop-standalone-libs-generic-hadoop3-$DSS_VERSION" \
    && cd /home/dataiku \
    && echo "+ Downloading Hadoop kit" \
    && curl -o "kit_hadoop.tgz" -sS "https://cdn.downloads.dataiku.com/public/studio/$DSS_VERSION/$DSSHADOOPKIT.tar.gz"

# Download Spark ( WG )
RUN DSSSPARKKIT="dataiku-dss-spark-standalone-$DSS_VERSION-$DSS_SPARK_VERSION-generic-hadoop3" \
    && cd /home/dataiku \
    && echo "+ Downloading Spark kit" \
    && curl -o "kit_spark.tgz" -sS "https://cdn.downloads.dataiku.com/public/studio/$DSS_VERSION/$DSSSPARKKIT.tar.gz"

# Entry point
WORKDIR /home/dataiku
USER dataiku

COPY run.sh /home/dataiku/

EXPOSE $DSS_PORT

CMD [ "/home/dataiku/run.sh" ]
