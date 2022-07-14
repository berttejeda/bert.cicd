From --platform=linux/amd64 rockylinux:8.6

# Define container user
ENV USER=bitcoin
ENV UID=5000
ENV GID=6000

# Create container user
RUN echo "===> Creating bitcoin user ..."  &&\
groupadd $USER -g $GID  &&\
useradd -m -d /home/$USER/ -u $UID -g $GID -s /bin/bash $USER

RUN yum -y update

RUN yum install -y curl bash ncurses

# Borrowed from steps outlined here: https://github.com/ruimarinho/docker-bitcoin-core/blob/master/22/Dockerfile
ENV TARGETPLATFORM=x86_64-linux-gnu
ENV BITCOIN_VERSION=22.0
ENV BITCOIN_DATA=/home/bitcoin/.bitcoin
ENV PATH=/opt/bitcoin-${BITCOIN_VERSION}/bin:$PATH

RUN for key in \
    0CCBAAFD76A2ECE2CCD3141DE2FFD5B1D88CA97D \
    152812300785C96444D3334D17565732E08E5E41 \
    0AD83877C1F0CD1EE9BD660AD7CC770B81FD22A8 \
    590B7292695AFFA5B672CBB2E13FC145CD3F4304 \
    28F5900B1BB5D1A4B6B6D1A9ED357015286A333D \
    637DB1E23370F84AFF88CCE03152347D07DA627C \
    CFB16E21C950F67FA95E558F2EEB9F5CC09526C1 \
    6E01EEC9656903B0542B8F1003DB6322267C373B \
    D1DBF2C4B96F2DEBF4C16654410108112E7EA81F \
    82921A4B88FD454B7EB8CE3C796C4109063D4EAF \
    9DEAE0DC7063249FB05474681E4AED62986CD25D \
    9D3CC86A72F8494342EA5FD10A41BDC3F4FAFF1C \
    74E2DEF5D77260B98BC19438099BAD163C70FBFA \
    B42F6819007F00F88E364FD4036A9C25BF357DD4 \
  ; do \
      gpg --batch --keyserver keyserver.ubuntu.com --recv-keys "$key" || \
      gpg --batch --keyserver keys.openpgp.org --recv-keys "$key" || \
      gpg --batch --keyserver pgp.mit.edu --recv-keys "$key" || \
      gpg --batch --keyserver keyserver.pgp.com --recv-keys "$key" || \
      gpg --batch --keyserver ha.pool.sks-keyservers.net --recv-keys "$key" || \
      gpg --batch --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys "$key" ; \
    done \
  && curl -SLO https://bitcoincore.org/bin/bitcoin-core-${BITCOIN_VERSION}/bitcoin-${BITCOIN_VERSION}-${TARGETPLATFORM}.tar.gz \
  && curl -SLO https://bitcoincore.org/bin/bitcoin-core-${BITCOIN_VERSION}/SHA256SUMS \
  && curl -SLO https://bitcoincore.org/bin/bitcoin-core-${BITCOIN_VERSION}/SHA256SUMS.asc \
  && gpg --verify SHA256SUMS.asc SHA256SUMS \
  && grep " bitcoin-${BITCOIN_VERSION}-${TARGETPLATFORM}.tar.gz" SHA256SUMS | sha256sum -c - \
  && tar -xzf *.tar.gz -C /opt \
  && rm *.tar.gz *.asc \
  && rm -rf /opt/bitcoin-${BITCOIN_VERSION}/bin/bitcoin-qt

# Install gosu.  https://github.com/tianon/gosu
ENV GOSU_VERSION=1.14
RUN curl -o /usr/local/bin/gosu -SL "https://github.com/tianon/gosu/releases/download/${GOSU_VERSION}/gosu-amd64" \
    && curl -o /usr/local/bin/gosu.asc -SL "https://github.com/tianon/gosu/releases/download/${GOSU_VERSION}/gosu-amd64.asc" \
    && gpg --verify /usr/local/bin/gosu.asc \
    && rm /usr/local/bin/gosu.asc \
    && rm -r /root/.gnupg/ \
    && chmod +x /usr/local/bin/gosu \
    # Verify that the binary works
    && gosu nobody true

ENV BITCOIN_DATA=/home/$USER/.bitcoin

RUN mkdir $BITCOIN_DATA && chown -R $USER:$USER $BITCOIN_DATA

RUN bitcoind -version | grep "Bitcoin Core version v${BITCOIN_VERSION}"

USER $USER

EXPOSE 8332 8333 18332 18333 18443 18444 38333 38332

VOLUME ["/home/bitcoin/.bitcoin"]

ENTRYPOINT ["bitcoind"]