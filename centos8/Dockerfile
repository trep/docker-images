#
# Docker image powering the Transport/Travel Search Web UI featuring OpenTREP
#
# File: https://github.com/trep/docker-images/blob/master/centos8/Dockerfile
# * On Docker Hub: https://hub.docker.com/repository/docker/opentrep/search-travel
#
# Docker image for CentOS 8 Stream:
# * GitHub: https://github.com/tgagor/docker-centos-stream
# * On Docker Hub: https://hub.docker.com/repository/docker/tgagor/centos-stream
#
FROM tgagor/centos-stream

LABEL authors="Denis Arnaud <denis.arnaud_github at m4x dot org>"
LABEL version="0.1"

#
ENV container docker
ENV HOME /root
ENV TREP_DIR /var/www/webapps/opentrep/trep
ENV TREP_BUILD_DIR ${HOME}/trep/build
ENV XAPIAN_DB ${TREP_DIR}/traveldb
ENV SQLITE_DB ${TREP_DIR}/sqlite_travel.db

# House keeping
RUN dnf clean all && dnf -y update

# Add the repository for PowerTools
RUN dnf -y install 'dnf-command(config-manager)'
RUN dnf config-manager --set-enabled powertools

# Add the repositories for EPEL support
RUN dnf -y install epel-release

# Install a few packages
RUN dnf -y install htop less file sudo rpmconf git bash-completion \
      jq curl

# C++ development
RUN dnf -y install less htop net-tools which sudo man vim \
	git-all wget curl file bash-completion keyutils \
	zlib-devel bzip2-devel gzip tar rpmconf yum-utils \
        autoconf automake libtool \
	langtable gcc gcc-c++ cmake m4 cppunit-devel \
	zeromq-devel czmq-devel cppzmq-devel openblas-devel \
	xapian-core-devel openssl-devel libffi-devel \
	readline-devel sqlite-devel mysql-devel \
	soci-mysql-devel soci-sqlite3-devel && \
  dnf -y install libicu-devel \
	protobuf-devel protobuf-compiler \
	python3-django python3-mod_wsgi \
	python38 python38-devel \
	geos-devel \
	graphviz ghostscript doxygen && \
  dnf -y install "tex(latex)" texlive-epstopdf-bin

# User environment
ADD bashrc ${HOME}/
RUN cat ${HOME}/bashrc >> ${HOME}/.bashrc && rm -f ${HOME}/bashrc

# Boost 1.73
RUN dnf -y copr enable denisarnaud/boost1.73 && \
    dnf -y install boost1.73-devel boost1.73-python3-devel

# SOCI
RUN mkdir -p /opt/soci && \
    git clone https://github.com/SOCI/soci.git /opt/soci/socigit && \
    mkdir -p /opt/soci/socigit/build/head
WORKDIR /opt/soci/socigit/build/head
RUN cmake -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release \
      -DSOCI_CXX11=ON -DSOCI_TESTS=OFF ../.. && \
    make && make install

# Set up of the environment for OpenTREP
WORKDIR ${HOME}
RUN trep_ver=$(curl -s https://api.github.com/repos/trep/opentrep/releases/latest | \
  jq -r ".tarball_url" | sed -e 's/.*v\([0-9.]\{3\}\)/\1/g') && \
  curl -L https://github.com/trep/opentrep/archive/v${trep_ver}.tar.gz \
    -o ${HOME}/opentrep-${trep_ver}.tar.gz && \
  tar zxf opentrep-${trep_ver}.tar.gz && \
  ln -s opentrep-${trep_ver} trep && \
  mkdir -p ${TREP_BUILD_DIR}
WORKDIR ${TREP_BUILD_DIR}

# Launch the build
RUN cmake -DCMAKE_INSTALL_PREFIX=${TREP_DIR} \
     -DCMAKE_BUILD_TYPE:STRING=Debug \
     -DBOOST_LIBRARYDIR=/usr/lib64/boost1.73 \
     -DBOOST_INCLUDEDIR=/usr/include/boost1.73 \
     -DBoost_ADDITIONAL_VERSIONS="1.73 1.73.0" \
     -DENABLE_TEST:BOOL=ON -DRUN_GCOV:BOOL=OFF \
     -DINSTALL_DOC:BOOL=OFF -DLIB_SUFFIX=64 ..
RUN make && make install

#
WORKDIR ${HOME}

# Launch the indexation
RUN ${TREP_DIR}/bin/opentrep-indexer -d ${XAPIAN_DB} -t sqlite -s ${SQLITE_DB}

# Check that the search works correctly
RUN ${TREP_DIR}/bin/opentrep-searcher -d ${XAPIAN_DB} -t sqlite -s ${SQLITE_DB} -q "nce sfo"

#
CMD ["/bin/bash"]

