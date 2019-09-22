#
# Docker image: https://github.com/trep/docker-images/blob/master/centos7/Dockerfile
# Docker image powering the Transport/Travel Search Web UI featuring OpenTREP
#
FROM docker.io/centos:7
MAINTAINER Denis Arnaud <denis.arnaud underscore fedora at m4x.org>

#
ENV container docker
ENV HOME /root
ENV TREP_BASE_DIR /opt/trep
ENV TREP_DIR ${TREP_BASE_DIR}/opentrep
ENV TREP_BUILD_DIR ${TREP_DIR}/build
ENV INSTALL_BASEDIR ${TREP_BASE_DIR}/deliveries
ENV TREP_VER 0.6.7
ENV XAPIANDB ${TREP_BASE_DIR}/xapiandb

# House keeping
RUN yum clean all && \
    yum -y update && \
    yum -y install epel-release && \
    yum -y install htop less file sudo rpmconf git bash-completion

# C++ development
RUN yum -y install gcc-c++ cmake cmake3 boost-devel xapian-core-devel \
    soci-mysql-devel soci-sqlite3-devel \
    readline-devel sqlite-devel mariadb-devel \
    libicu-devel protobuf-devel protobuf-compiler

# Python development
RUN yum -y install python-devel python36-devel

# Documentation tools
RUN yum -y install doxygen ghostscript "tex(latex)"

# User environment
ADD bashrc $HOME/
RUN cat $HOME/bashrc >> $HOME/.bashrc && rm -f $HOME/bashrc

# Set up of the environment for OpenTREP
RUN mkdir -p ${TREP_BASE_DIR}
WORKDIR ${TREP_BASE_DIR}
RUN git clone https://github.com/trep/opentrep.git
WORKDIR ${TREP_DIR}
RUN mkdir -p build
WORKDIR ${TREP_BUILD_DIR}

# Launch the build
RUN cmake3 -DCMAKE_INSTALL_PREFIX=${INSTALL_BASEDIR}/opentrep-${TREP_VER} \
   -DCMAKE_BUILD_TYPE:STRING=Debug -DINSTALL_DOC:BOOL=OFF \
   -DRUN_GCOV:BOOL=OFF -DLIB_SUFFIX=64 ..
RUN make && make install

# Launch the indexation
RUN ./opentrep/opentrep-indexer --xapiandb ${XAPIANDB}

# Check that the search works correctly
RUN ./opentrep/opentrep-searcher --xapiandb ${XAPIANDB} -q nce sfo

#
CMD ["/bin/bash"]

