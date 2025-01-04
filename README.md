## Introduction

Streamlining the environment for junior web development with Docker is an exciting and rewarding project. Docker simplifies the development process by providing consistent and isolated environments for building and deploying applications. This not only reduces the "it works on my machine" problem but also enhances collaboration among team members. Through this project, junior developers will gain hands-on experience with containerization, which is an essential skill in modern web development.

### Compiled Image

Check the compiled image on Docker Hub: [https://hub.docker.com/r/ramaid/image](https://hub.docker.com/r/ramaid/image)

### Tips

Based on information from [install-php-extension](https://github.com/mlocati/docker-php-extension-installer), we can install `oci8` and `pdo_oci` extensions for Oracle Database. The following is an example of how to install these extensions:

```Dockerfile
IPE_INSTANTCLIENT_BASIC=1 install-php-extensions oci8 pdo_oci
```
