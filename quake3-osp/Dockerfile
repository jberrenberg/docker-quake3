FROM jberrenberg/quake3

MAINTAINER jberrenberg

# add osp 
RUN curl -O http://osp.dget.cc/orangesmoothie/downloads/osp-Quake3-1.03a_full.zip && \
  unzip osp-Quake3-1.03a_full.zip -d /opt/quake3 && \
  rm osp-Quake3-1.03a_full.zip

ENV FS_GAME osp
