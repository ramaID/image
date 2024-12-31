## Introduction

Streamlining the environment for junior web development with Docker is an exciting and rewarding project. Docker simplifies the development process by providing consistent and isolated environments for building and deploying applications. This not only reduces the "it works on my machine" problem but also enhances collaboration among team members. Through this project, junior developers will gain hands-on experience with containerization, which is an essential skill in modern web development.

### Uncompiled Image

There is an issue with GitHub Actions, so if you need a Docker image with the OCI8 extension, it needs to be built manually.

```bash
docker build -t php-oci8 --build-arg PHP_VERSION=8.1 -f src/oci8/Dockerfile .
```

### Compiled Image

Check the compiled image on Docker Hub: [https://hub.docker.com/r/ramaid/image](https://hub.docker.com/r/ramaid/image)
