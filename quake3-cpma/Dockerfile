FROM jberrenberg/quake3:latest

LABEL maintainer="jberrenberg"

ADD https://cdn.playmorepromode.com/files/cpma/cpma-1.52-nomaps.zip /tmp/cpma-nomaps.zip 
ADD https://cdn.playmorepromode.com/files/cpma-mappack-full.zip /tmp/cpma-mappack-full.zip  

USER root
RUN \
  unzip /tmp/cpma-nomaps.zip -d /home/ioq3srv/ioquake3 && \
  rm /tmp/cpma-nomaps.zip && \
  unzip /tmp/cpma-mappack-full.zip  -d /home/ioq3srv/ioquake3/baseq3 && \
  rm /tmp/cpma-mappack-full.zip
USER ioq3srv

ENTRYPOINT ["/home/ioq3srv/ioquake3/ioq3ded.x86_64", "+set", "dedicated", "2", "+set", "fs_game", "cpma"]
