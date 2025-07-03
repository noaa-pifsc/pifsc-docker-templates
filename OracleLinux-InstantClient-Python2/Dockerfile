FROM oraclelinux:8

# Install Oracle Instant Client
RUN dnf -y install oracle-instantclient-release-el8 \
    && dnf -y install \
        oracle-instantclient-basic \
        oracle-instantclient-devel \
        oracle-instantclient-sqlplus \
        oracle-instantclient-tools
        
# Install Python 2.7 and pip
RUN dnf -y install \
    python2 \
    libaio \
    wget \
    unzip \
    && curl -O https://bootstrap.pypa.io/pip/2.7/get-pip.py \
    && python2 get-pip.py \
    && rm -f get-pip.py \
    && ln -s /usr/bin/python2 /usr/bin/python
        
# Install dependencies for cx_Oracle for Python 2.7
RUN dnf -y install gcc \
    redhat-rpm-config \
    python2-devel \
    libaio-devel

# Install dependencies for Dev Container
RUN dnf -y git

# Clean up DNF cache to reduce image size
RUN dnf clean all

# Install python requirements
RUN pip2 install requirements.txt

# Set up Oracle environment variables
ENV LD_LIBRARY_PATH=/usr/lib/oracle/21/client64/lib:$LD_LIBRARY_PATH \
    PATH=$PATH:/usr/lib/oracle/21/client64/bin \
    ORACLE_HOME=/usr/lib/oracle/21/client64 \
    TNS_ADMIN=/usr/lib/oracle/21/network/admin

WORKDIR /srv/longline-er-data-stream

# default CMD for container oraclelinux:8 is bash