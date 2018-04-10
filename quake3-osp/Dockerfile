FROM jberrenberg/quake3:latest

MAINTAINER jberrenberg v1.2

USER root
# add osp 
RUN \
  echo "# INSTALL DEPENDENCIES ##########################################" && \
  wget http://osp.dget.cc/orangesmoothie/downloads/osp-Quake3-1.03a_full.zip && \
  su ioq3srv -c "unzip osp-Quake3-1.03a_full.zip -d ~/ioquake3" && \
  echo "# CLEAN UP ######################################################" && \
  rm osp-Quake3-1.03a_full.zip

USER ioq3srv

ENTRYPOINT ["/home/ioq3srv/ioquake3/ioq3ded.x86_64", "+set", "fs_game", "osp"]
