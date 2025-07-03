FROM oraclelinux:8

# Default ARCH to x86_64 if not passed
ARG ARCH=x86_64
ENV ARCH=${ARCH}
RUN echo "Using architecture: ${ARCH}"

# Oracle Instant Client env
ENV ORACLE_HOME=/opt/oracle/instantclient \
    LD_LIBRARY_PATH=/opt/oracle/instantclient \
    PATH=/opt/oracle/instantclient:$PATH

# Install dependencies
RUN dnf install -y \
    oraclelinux-developer-release-el8 \
    gcc \
    libaio-devel \
    libnsl \
    python39-devel \
    python39-pip \
    unzip \
    curl \
    git && \
    dnf clean all

# Download Instant Client based on ARCH
RUN set -eux; \
    if [ "$ARCH" = "x86_64" ]; then \
    curl -O https://download.oracle.com/otn_software/linux/instantclient/1927000/oracle-instantclient19.27-basic-19.27.0.0.0-1.el8.x86_64.rpm && \
    curl -O https://download.oracle.com/otn_software/linux/instantclient/1927000/oracle-instantclient19.27-sqlplus-19.27.0.0.0-1.el8.x86_64.rpm && \
    curl -O https://download.oracle.com/otn_software/linux/instantclient/1927000/oracle-instantclient19.27-tools-19.27.0.0.0-1.el8.x86_64.rpm; \
    elif [ "$ARCH" = "aarch64" ]; then \
    curl -O https://download.oracle.com/otn_software/linux/instantclient/1927000/oracle-instantclient19.27-basic-19.27.0.0.0-1.el8.aarch64.rpm && \
    curl -O https://download.oracle.com/otn_software/linux/instantclient/1927000/oracle-instantclient19.27-sqlplus-19.27.0.0.0-1.el8.aarch64.rpm && \
    curl -O https://download.oracle.com/otn_software/linux/instantclient/1927000/oracle-instantclient19.27-tools-19.27.0.0.0-1.el8.aarch64.rpm; \
    else \
    echo "Unsupported ARCH: $ARCH" && exit 1; \
    fi && \
    rpm -ivh oracle-instantclient*.rpm && \
    rm oracle-instantclient*.rpm

# Check OS version and add Microsoft repo
RUN curl -sSL -O https://packages.microsoft.com/config/rhel/8/packages-microsoft-prod.rpm && \
    dnf install -y packages-microsoft-prod.rpm && \
    rm packages-microsoft-prod.rpm

# Remove conflicting ODBC packages (if present)
RUN dnf remove -y unixODBC-utf16 unixODBC-utf16-devel || true

# Install ODBC Driver 18 and optional tools
RUN ACCEPT_EULA=Y dnf install -y msodbcsql18 && \
    ACCEPT_EULA=Y dnf install -y mssql-tools18 && \
    echo 'export PATH="$PATH:/opt/mssql-tools18/bin"' >> ~/.bashrc && \
    dnf install -y unixODBC-devel && \
    dnf clean all

# Python setup
RUN ln -s /usr/bin/python3.9 /usr/bin/python && \
    ln -s /usr/bin/pip3.9 /usr/bin/pip && \
    python -m pip install --upgrade pip

# Create a non-root user and set permissions
RUN useradd -ms /bin/bash appuser && \
    mkdir -p /workspaces/lotus-observer-data-transfer && \
    chown -R appuser:appuser /workspaces/lotus-observer-data-transfer

# Switch to appuser before installing Python dependencies
USER appuser

# Copy requirements.txt and install Python dependencies as appuser
COPY --chown=appuser:appuser requirements.txt /workspaces/lotus-observer-data-transfer/requirements.txt
RUN pip install --no-cache-dir --user -r /workspaces/lotus-observer-data-transfer/requirements.txt

# Copy application source code into the image
COPY src/ /workspaces/lotus-observer-data-transfer/src/

# Set the default command to run main.py
CMD ["python", "/workspaces/lotus-observer-data-transfer/src/main.py"]


