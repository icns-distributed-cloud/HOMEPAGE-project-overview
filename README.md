# Overview homepage of the distributed cloud project
This is an overview homepage of The distributed cloud project, which is an open source project. This hompage is based on Flask.

Link: [Open distributed cloud homepage](http://opendc.icnslab.net/)

## Run this project on a container
To run this homepage on a container, the following steps are needed.

### 1. Change the DNS settings of Docker daemon (Option)
Google DNS server is sometimes unreachable and containers cannot access the internet. In my case, I'm using campus network. The network has primary and secondary DSN servers. Therefore , I need to change default DNS server of docker from Google DNS server to my campus DNS server. Please refer to [Fix Docker's networking DNS config](https://development.robinwinslow.uk/2016/06/23/fix-docker-networking-dns/) (accessed on 25 July 2019)

#### 1.1. Create this file

/etc/docker/daemon.json

    {
        "dns": ["x.x.x.x", "x.x.x.x"]
    }

#### 1.2. Restart the docker service

    sudo service docker restart

## 2. Dockerize this project
### 2.1 Create a directory to build container image
A directory is needed to copy some files for building container image. For example:

    /home/jack/mydockerfile

### 2.2 Create "Dockerfile"
This is the Dockerfile for this homepage. It is located in /home/jack/mydockerfile (This is an example path).

    FROM python
    MAINTAINER [name] "[email]"

    RUN git clone https://github.com/icns-distributed-cloud/overview-homepage.git /app
    COPY ./requirements.txt /app
    COPY ./entrypoint.sh /
    WORKDIR /app
    RUN pip install --upgrade pip
    RUN pip install -r requirements.txt
    RUN chmod +x /entrypoint.sh
    ENTRYPOINT ["/entrypoint.sh"]
    EXPOSE 80

### 2.3 Create "entrypoint.sh"
To run flask app, a script, entrypoint.sh is needed. This is the entrypoint.sh to run this homepage. It is located in /home/jack/mydockerfile (This is an example path).

    #!/bin/bash
  
    python /app/app.py

### 2.4 Build container image
To build a container image, the below command can be used.

    docker build --network=host -t opendc_web:latest .

### 2.5 Run a container based on the built container image
To run this homepage, the below command can be used. Port 80 is opened and forwarded to 5000.

    docker run -it --name opendc_website -p 80:5000 opendc_web:latest

