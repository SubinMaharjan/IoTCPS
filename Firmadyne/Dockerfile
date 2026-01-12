# ------------------------------------------------------------
# Modified by Subin Maharjan (maha1364@vandals.uidaho.edu)
# Course CYB504: IoT and CPS Security
# University of Idaho
# Professor: Dr. Bhaskar Rimal
# ------------------------------------------------------------
# Reference: https://github.com/firmadyne/firmadyne/tree/master

# docker build -t firmadyne . && docker run -v $(pwd)/data:/firmadyne/images -it --privileged firmadyne
FROM --platform=amd64 ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV FIRMADYNE_INSTALL_DIR=/firmadyne

# Update packages
RUN apt-get update && apt-get upgrade -y && apt-get install -y sudo adduser

# Create firmadyne user
RUN useradd -m firmadyne
RUN echo "firmadyne:firmadyne" | chpasswd && adduser firmadyne sudo

RUN apt-get install -y busybox-static fakeroot git \
dmsetup kpartx netcat-openbsd nmap python3-psycopg2 snmp \
uml-utilities util-linux vlan postgresql wget qemu-system-arm \
python3 python3-pip python-is-python3 qemu-system-mips qemu-system-x86 qemu-utils vim unzip \
lzma liblzma-dev lzop liblzo2-dev libmagic1 fdisk

RUN git clone https://github.com/firmadyne/firmadyne.git

RUN cd ${FIRMADYNE_INSTALL_DIR} && \
  wget https://github.com/ReFirmLabs/binwalk/archive/refs/tags/v2.3.4.tar.gz && \
  tar -xf v2.3.4.tar.gz && \
  cd binwalk-2.3.4 && \
  sed -i 's/^install_ubireader//g;s/^install_sasquatch//g' deps.sh && \
  git clone --quiet --depth 1 --branch "master" https://github.com/devttys0/sasquatch && \
  cd sasquatch && \
  wget https://github.com/devttys0/sasquatch/pull/51.patch && patch -p1 <51.patch && \
  ./build.sh && cd .. && \
  echo y | ./deps.sh && \
  python3 setup.py install && \
  cd .. && \
  pip3 install git+https://github.com/ahupp/python-magic && \
  pip install git+https://github.com/sviehb/jefferson && \
  service postgresql start && \
  sudo -u postgres createuser firmadyne && \
  sudo -u postgres createdb -O firmadyne firmware && \
  sudo -u postgres psql -d firmware < ./database/schema && \
  echo "ALTER USER firmadyne PASSWORD 'firmadyne'" | sudo -u postgres psql && \
  ./download.sh && \
  mv firmadyne.config firmadyne.config.orig && \
  echo "#!/bin/sh\nFIRMWARE_DIR=$(pwd)/" > firmadyne.config && \
  cat firmadyne.config.orig >> firmadyne.config && \
  sudo chown -R firmadyne:firmadyne $FIRMADYNE_INSTALL_DIR && \
  echo y | sudo python3 -m pip uninstall python-magic && \
  sudo python3 -m pip install python-magic

RUN cd ${FIRMADYNE_INSTALL_DIR}/sources && \
  git clone https://github.com/firmadyne/console.git && \
  git clone https://github.com/firmadyne/extractor.git && \
  git clone https://github.com/firmadyne/libnvram.git && \
  git clone https://github.com/firmadyne/scraper.git

USER firmadyne
ENTRYPOINT ["/firmadyne/startup.sh"]
CMD ["/bin/bash"]
