Creating and Running OpenTREP Docker Containers
===============================================

# References
* [OpenTREP GitHub repository](https://github.com/trep/opentrep)
* [OpenTravelData (OPTD) GitHub repository](https://github.com/opentraveldata/opentraveldata)
* [Pre-built images on Docker Cloud](https://cloud.docker.com/u/opentrep/repository/docker/opentrep/search-travel)
* [Pre-built images on Quay.io](https://quay.io/repository/trep/opentrep)

# Launch a container with pre-built Docker images
* Download the Docker image:
```bash
$ docker pull opentrep/search-travel:centos7
```

* Parse a transport/travel request with the container:
```bash
$ docker run -t opentrep/search-travel:centos7 "opentrep-search -q nce sfo"
```

# Contribute a custom Docker image
* Build the Docker image:
```bash
$ docker build -t opentrep/search-travel:centos7beta centos7
```

* Login to the remote Docker repository, for instance with
  [Docker Hub](https://cloud.docker.com):
```bash
$ docker login
```

* Submit the Docker image:
```bash
$ docker push opentrep/search-travel:centos7beta
```


