FROM ubuntu:xenial as builder
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update \
 && apt-get -y upgrade \
 && apt-get -y install build-essential debhelper git python3-all python3-setuptools help2man bash-completion docbook-xsl xsltproc

ARG VERSION=1.1.0

RUN git clone --recurse-submodules https://github.com/LINBIT/linstor-api-py /linstor-api-py
WORKDIR /linstor-api-py
RUN git checkout v${VERSION} \
 && make debrelease \
 && mv ./dist/python-linstor-${VERSION}.tar.gz ../python-linstor_${VERSION}.orig.tar.gz \
 && tar -C / -xvf /python-linstor_${VERSION}.orig.tar.gz
WORKDIR /python-linstor-${VERSION}
RUN dpkg-buildpackage -us -uc

RUN make install

RUN git clone https://github.com/LINBIT/linstor-client.git /linstor-client
WORKDIR /linstor-client
RUN git checkout v${VERSION} \
 && make debrelease \
 && mv dist/linstor-client-${VERSION}.tar.gz /linstor-client_${VERSION}.orig.tar.gz \
 && tar -C / -xvf /linstor-client_${VERSION}.orig.tar.gz
WORKDIR /linstor-client-${VERSION}
RUN dpkg-buildpackage -us -uc

FROM ubuntu:xenial
COPY --from=builder /python-linstor_*_all.deb /linstor-client_*_all.deb /packages/
RUN apt-get update \
 && apt-get install -y python3-all python3-natsort \
 && dpkg -i packages/*.deb \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*
