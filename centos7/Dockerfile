#
# Docker image: https://github.com/trep/docker-images/blob/master/centos7/Dockerfile
# Docker image powering the Transport/Travel Search Web UI featuring OpenTREP
#
FROM docker.io/centos:7
MAINTAINER Denis Arnaud <denis.arnaud underscore fedora at m4x.org>

#
ENV container docker
ENV HOME /root
ENV TREP_DIR /var/www/webapps/opentrep/trep
ENV TREP_BUILD_DIR ${HOME}/trep/build
ENV TREP_VER 0.6.7
ENV XAPIAN_DB ${TREP_DIR}/traveldb
ENV SQLITE_DB ${TREP_DIR}/sqlite_travel.db

# House keeping
RUN yum clean all && \
    yum -y update && \
    yum -y install epel-release && \
    yum -y install htop less file sudo rpmconf git bash-completion

# C++ development
RUN yum -y install gcc-c++ make cmake cmake3 \
    boost-devel xapian-core-devel \
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
RUN git clone https://github.com/trep/opentrep.git ${HOME}/trep
RUN mkdir -p ${TREP_BUILD_DIR}
WORKDIR ${TREP_BUILD_DIR}

# Launch the build
RUN cmake3 -DCMAKE_INSTALL_PREFIX=${TREP_DIR} \
   -DCMAKE_BUILD_TYPE:STRING=Debug -DINSTALL_DOC:BOOL=OFF \
   -DRUN_GCOV:BOOL=OFF -DLIB_SUFFIX=64 ..
RUN make && make install

#
WORKDIR ${HOME}

# Launch the indexation
RUN ${TREP_DIR}/bin/opentrep-indexer -d ${XAPIAN_DB} -t sqlite -s ${SQLITE_DB}

# Check that the search works correctly
RUN ${TREP_DIR}/bin/opentrep-searcher -d ${XAPIAN_DB} -t sqlite -s ${SQLITE_DB} -q "nce sfo"

#
CMD ["/bin/bash"]


