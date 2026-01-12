# By: Chadwick Goodall good0206@vandals.uidaho
# get base ubuntu (bionic; 18.04) image
FROM ubuntu:latest
WORKDIR /home/ubuntu

RUN apt update -y
RUN apt upgrade -y

# utilities
RUN apt install -y git
RUN apt install -y vim

# Python install (latest version available)
RUN apt install -y python3 python3-pip
# necessary tooling for binwalk
RUN apt install rustup
RUN rustup default stable
# pull tools from github
RUN git clone https://github.com/ReFirmLabs/binwalk.git


########### BinwalkV3 setup ##########
RUN ./binwalk/dependencies/ubuntu.sh
# compiled binary located at binwalk/target/release/binwalk
RUN cd binwalk && cargo build --release && cd /home/ubuntu/
# symlink binary
RUN ln -s /home/ubuntu/binwalk/target/release/binwalk /bin/binwalk

# generate requirements.txt for documentation purposes
RUN python3 -m pip freeze > requirements.txt

# make mount directory for firmware image volume
RUN mkdir firmware
