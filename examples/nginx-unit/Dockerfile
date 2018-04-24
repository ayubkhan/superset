FROM python:3.6-slim

ENV UNIT_VERSION 0.1

# Superset version
ARG SUPERSET_VERSION=0.24.0

# Configure environment
ENV LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    PYTHONPATH=/etc/superset:/home/superset:$PYTHONPATH \
    SUPERSET_VERSION=${SUPERSET_VERSION} \
    SUPERSET_HOME=/var/lib/superset

RUN useradd -U -m superset && \
    mkdir /etc/superset  && \
    mkdir ${SUPERSET_HOME} && \
    chown -R superset:superset /etc/superset && \
    chown -R superset:superset ${SUPERSET_HOME} && \
    apt-get update && apt-get install -y \
                gcc \
                gettext \
                mysql-client libmysqlclient-dev \
                sqlite3 build-essential wget curl \
        libffi-dev \
        libldap2-dev \
        libpq-dev \
        libsasl2-dev \
        libssl-dev \
        python3-dev \
        python3-pip \
        --no-install-recommends && rm -rf /var/lib/apt/lists/* \
&& cd /tmp && wget  -O - "http://unit.nginx.org/download/unit-$UNIT_VERSION.tar.gz" | tar  xvz   \
&& cd unit-$UNIT_VERSION \
&& ./configure --prefix=/usr  --modules=lib --control='*:8000' --log=/dev/stdout --pid=/var/run/unitd.pid \
&& ./configure python --module=py36\
&& make install \
&& mkdir /srv/app \
&& pip install mysqlclient \
&& pip install flask-cors==3.0.3 \
        flask-mail==0.9.1 \
        flask-oauth==0.12 \
        flask_oauthlib==0.9.3 \
        gevent==1.2.2 \
        impyla==0.14.0 \
        mysqlclient==1.3.7 \
        psycopg2==2.6.1 \
        pyathenajdbc==1.2.0 \
        pyhive==0.5.0 \
        pyldap==2.4.28 \
        redis==2.10.5 \
        sqlalchemy-redshift==0.5.0 \
        sqlalchemy-clickhouse==0.1.1.post3 \
        Werkzeug==0.12.1 \
        superset==${SUPERSET_VERSION} \
&& rm -rf /tmp/unit-$UNIT_VERSION \
&& apt-get remove --auto-remove -y  build-essential wget 

ADD  wsgi.py /srv/app
ADD  config.json /srv/app

WORKDIR /srv/app

CMD ["/usr/sbin/unitd", "--no-daemon"]

# Configure Filesystem
COPY superset-init /usr/local/bin
VOLUME /home/superset \
       /etc/superset \
       /var/lib/superset
WORKDIR /home/superset

EXPOSE 8000 8088
