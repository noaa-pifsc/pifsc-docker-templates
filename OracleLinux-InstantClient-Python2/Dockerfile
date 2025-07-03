FROM oraclelinux:8

# Install Oracle Instant Client
RUN dnf -y install oracle-instantclient-release-el8 \
    && dnf -y install \
        oracle-instantclient-basic \
        oracle-instantclient-devel \
        oracle-instantclient-sqlplus \
        oracle-instantclient-tools \
        # install git for dev
        git

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

RUN dnf clean all

# Install cx_Oracle for Python 2.7 connection
RUN pip2 install 'cx_Oracle==7.3.0' \
    'ssh==1.8.0' \
    'pytz==2017.3' \
    'reportlab==2.7' \
    'requests' \
    'zeep' \
    # Install linting tools
    'autopep8<1.6.0' \
    'pycodestyle<=2.7.0'

# Environment variables
ENV LD_LIBRARY_PATH=/usr/lib/oracle/21/client64/lib:$LD_LIBRARY_PATH \
    PATH=$PATH:/usr/lib/oracle/21/client64/bin \
    ORACLE_HOME=/usr/lib/oracle/21/client64 \
    TNS_ADMIN=/usr/lib/oracle/21/network/admin

WORKDIR /srv/longline-er-data-stream

# default CMD for container oraclelinux:8 is bash