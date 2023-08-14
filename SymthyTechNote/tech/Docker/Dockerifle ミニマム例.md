
```Dockerfile
FROM oraclelinux:7-slim
RUN yum update -y && \
  yum-config-manager --disable ol7_ociyum_config && \
  yum install -y gcc curl zip unzip gunzip hostname perl perl-Time-HiRes
```

