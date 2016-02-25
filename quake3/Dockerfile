FROM alpine:latest
MAINTAINER jberrenberg v1.0

# add the user that runs the process
RUN adduser ioq3srv -D

# to reduce image size all build and cleanup steps are performed in one docker layer
RUN \
  echo "# INSTALL DEPENDENCIES ##########################################" && \
  apk --no-cache add curl g++ gcc git make && \
  mkdir -p /tmp/build && \
  echo "# FETCH INSTALLATION FILES ######################################" && \
  curl https://raw.githubusercontent.com/ioquake/ioq3/master/misc/linux/server_compile.sh -o /tmp/build/compile.sh && \
  curl http://ioquake3.org/data/quake3-latest-pk3s.zip --referer http://ioquake3.org/extras/patch-data/ -o /tmp/build/quake3-latest-pk3s.zip && \
  echo "# NOW THE INSTALLATION ##########################################" && \
  echo "y" | su ioq3srv -c "sh /tmp/build/compile.sh" && \
  unzip /tmp/build/quake3-latest-pk3s.zip -d /tmp/build/ && \
  su ioq3srv -c "cp -r /tmp/build/quake3-latest-pk3s/* ~/ioquake3" && \
  ln -s /pak0.pk3 /home/ioq3srv/ioquake3/baseq3/pak0.pk3 && \
  echo "# CLEAN UP ######################################################" && \
  apk del curl g++ gcc git make && \
  rm -rf /tmp/build/
 
USER ioq3srv

EXPOSE 27960/udp

ENTRYPOINT ["/home/ioq3srv/ioquake3/ioq3ded.x86_64"]
