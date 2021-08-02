Creating and Running OpenTREP Containers
========================================

[![Docker Cloud build status](https://img.shields.io/docker/cloud/build/infrahelpers/search-travel)](https://hub.docker.com/repository/docker/infrahelpers/search-travel/general)
[![Container repository on Quay](https://quay.io/repository/opentrep/search-travel/status "Container repository on Quay")](https://quay.io/repository/opentrep/search-travel)

# Introduction
[That project](https://github.com/trep/docker-images)
produces container (_e.g._, Docker) images, hosted on
[dedicated public Docker Cloud site](https://cloud.docker.com/u/bigdatadevelopment/repository/docker/infrahelpers/travel-search).
Those container images are intended to bring Linux-based ready-to-use
environment for OpenTREP contributors.

## References
* [OpenTREP container images (this repository)](https://github.com/trep/docker-images)
* [OpenTREP repository](https://github.com/trep/opentrep)
* [OpenTravelData (OPTD) repository](https://github.com/opentraveldata/opentraveldata)
* [Pre-built images on Docker Cloud](https://cloud.docker.com/u/opentrep/repository/docker/infrahelpers/search-travel)
* [Pre-built images on Quay.io](https://quay.io/repository/trep/opentrep)

# Usage

* Download the container image:
```bash
$ docker pull infrahelpers/search-travel:latest
```

* Parse a transport/travel request with the container:
```bash
$ docker run -t infrahelpers/search-travel:latest "opentrep-search -q nce sfo"
```

# Contribute a custom container image
* Get the
  [latest OpenREP release](https://github.com/trep/opentrep/releases/latest):
```bash
$ trep_ver=$(curl -s https://api.github.com/repos/trep/opentrep/releases/latest | jq -r ".tarball_url" | sed -e 's/.*v\([0-9.]\{3\}\)/\1/g')
$ curl -L https://github.com/trep/opentrep/archive/v${trep_ver}.tar.gz -o opentrep-${trep_ver}.tar.gz
```

* Build the container image:
```bash
$ docker build -t infrahelpers/search-travel:latest centos8
```

* Login to the remote Docker repository, for instance:
  + with [Docker Hub](https://cloud.docker.com):
```bash
$ docker login docker.io
Authenticating with existing credentials...
Login Succeeded
```
  + with [Quay.io](https://quay.io):
```bash
$ docker login quay.io
Authenticating with existing credentials...
Login Succeeded
```

* Submit the container image:
  + To Docker Cloud/Hub:
```bash
$ docker push infrahelpers/search-travel:latest
```
  + To Quay.io:
```bash
$ docker tag infrahelpers/search-travel:latest quay.io/opentrep/search-travel:latest
$ docker push quay.io/opentrep/search-travel:latest
```

* Further contributions are always welcome:
  + [Pull requests](https://github.com/trep/docker-images/pulls)
  + [Detection of issues](https://github.com/trep/docker-images/issues)

