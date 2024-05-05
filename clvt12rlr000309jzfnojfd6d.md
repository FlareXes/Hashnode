---
title: "Docker: Why It's the Best Tool I've Ever Learned?"
seoDescription: "Learn why Docker is a must-have tool for limitless possibilities in self-hosting, LLMs, virtual machines, network protocols, and container management"
datePublished: Sun May 05 2024 04:23:46 GMT+0000 (Coordinated Universal Time)
cuid: clvt12rlr000309jzfnojfd6d
slug: docker-why-its-the-best-tool-ive-ever-learned
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1714881235484/3f0fe374-823d-478c-9919-249bd50f528b.png
tags: ai, cloud, docker, networking, containers, self-hosted, llm

---

If you were to ask me, "What are the five most important technologies I've learned in the last five years?" then Docker is definitely one of them. Docker is one of those toolkits that make things limitless; I can do whatever I want, just like Thanos did with one snap. I'm not exaggerating. I don't know why I didn't cover about this before.

Today, I won't delve into how organizations utilize Docker for all their needs. Instead, I'll demonstrate how you, as an individual, can leverage Docker for a multitude of purposes.

## Prerequisites

1. Basic understanding of Docker
    
2. Active Docker instance
    

## The Realm of Self-Hosting

Self-hosting is a wild ride. Here, you must decide what you want to self-host and why. Do you desire an offline ChatGPT or a reverse proxy to manage your traffic? It has plenty of that and of course we are going to discuss some of them today. Self-hosting involves running services on your infrastructure instead of relying on external providers. This grants you control over data and privacy, brings cost savings, and crucially, enhances your skill set.

### Where to find self-hosting alternatives

1. Explore [Awesome-Selfhosted](https://github.com/awesome-selfhosted/awesome-selfhosted) GitHub Repository, Their are plenty of options.
    
2. Check if the products you use offer Docker images for self-hosting.
    
3. Try searching with the term "Self-host &lt;product name&gt;" for more options.
    

## Ollama: Self-Hosting LLMs like ChatGPT

Ollama is a streamlined tool designed for running LLMs (Large Language Models) locally. Ollama offers a wide range of models, including **Mixtral**, **Llama-3**, **CodeLlama**, and more. Getting started with Ollama is straightforward.

#### Step 1: Pull ollama docker images and start the container

```bash
docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
```

#### Step 2: Run any model ollama support like llama2

```bash
docker exec -it ollama ollama run llama2
```

Try different models at [Ollama library](https://ollama.com/library).

### GPU Support

If you have an Nvidia graphics card, you can leverage GPU processing power within Docker containers. This is particularly beneficial for tasks requiring intensive computation, such as hosting a Large Language Model (LLM). I recommend following the official Nvidia guide for adding GPU support, as the steps may vary over time. Check out the [NVIDIA Container Toolkit Installation](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html) guide for detailed instructions.

* **Start the container with GPU using** `--gpus=all`
    
    ```bash
    docker run -d --gpus=all -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
    ```
    

If You Like a User-Friendly, ChatGPT-Style Web Interface for Ollama. Then [Ollama-WebUI](https://github.com/open-webui/open-webui) is a place to go, yes you can also self-host ChatGPT like Web UI. You should also check out **Stephen M. Walker II** blog post on [*Ollama: Easily run LLMs locally*](https://klu.ai/glossary/ollama).

## ownCloud: Your Personal Google Drive

Absolutely! Just bear in mind that ownCloud stores data in Docker volumes, not on the standard file system. ownCloud offers numerous additional benefits, including file encryption (unlike Google Drive), OneDrive-like file sync, and Multi-Factor Authentication. Check out the ownCloud [website](https://owncloud.com/features/) for a comprehensive feature list.

### Let's Setup ownCloud

**Step 1:** Create a new project directory.

```bash
mkdir owncloud-docker-server
cd owncloud-docker-server
```

**Step 2:** Create a file `docker-compose.yml` under project's directory.

```yml
version: "3"

volumes:
  files:
    driver: local
  mysql:
    driver: local
  redis:
    driver: local

services:
  owncloud:
    image: owncloud/server:${OWNCLOUD_VERSION}
    container_name: owncloud_server
    restart: always
    ports:
      - ${HTTP_PORT}:8080
    depends_on:
      - mariadb
      - redis
    environment:
      - OWNCLOUD_DOMAIN=${OWNCLOUD_DOMAIN}
      - OWNCLOUD_TRUSTED_DOMAINS=${OWNCLOUD_TRUSTED_DOMAINS}
      - OWNCLOUD_DB_TYPE=mysql
      - OWNCLOUD_DB_NAME=owncloud
      - OWNCLOUD_DB_USERNAME=owncloud
      - OWNCLOUD_DB_PASSWORD=owncloud
      - OWNCLOUD_DB_HOST=mariadb
      - OWNCLOUD_ADMIN_USERNAME=${ADMIN_USERNAME}
      - OWNCLOUD_ADMIN_PASSWORD=${ADMIN_PASSWORD}
      - OWNCLOUD_MYSQL_UTF8MB4=true
      - OWNCLOUD_REDIS_ENABLED=true
      - OWNCLOUD_REDIS_HOST=redis
    healthcheck:
      test: ["CMD", "/usr/bin/healthcheck"]
      interval: 30s
      timeout: 10s
      retries: 5
    volumes:
      - files:/mnt/data

  mariadb:
    image: mariadb:10.11 # minimum required ownCloud version is 10.9
    container_name: owncloud_mariadb
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=owncloud
      - MYSQL_USER=owncloud
      - MYSQL_PASSWORD=owncloud
      - MYSQL_DATABASE=owncloud
      - MARIADB_AUTO_UPGRADE=1
    command: ["--max-allowed-packet=128M", "--innodb-log-file-size=64M"]
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-u", "root", "--password=owncloud"]
      interval: 10s
      timeout: 5s
      retries: 5
    volumes:
      - mysql:/var/lib/mysql

  redis:
    image: redis:6
    container_name: owncloud_redis
    restart: always
    command: ["--databases", "1"]
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    volumes:
      - redis:/data
```

**Step 3:** Create a `.env` configuration file, which contains the required configuration settings.

```bash
cat << EOF > .env
OWNCLOUD_VERSION=10.14
OWNCLOUD_DOMAIN=localhost:8080
OWNCLOUD_TRUSTED_DOMAINS=localhost
ADMIN_USERNAME=admin
ADMIN_PASSWORD=admin
HTTP_PORT=8080
EOF
```

**Step 4:** Edit `.env` and `docker-compose.yml` as you wise like passwords of admin & mysql account or port number etc.

**Step 5:** Then build and start the container.

```bash
docker-compose up -d
```

**Step 6:** Visit `http://localhost:8080` in browser and start using your ownCloud.

For more information about OwnCloud, visit the [OwnCloud Deployment Documentation](https://doc.owncloud.com/server/next/admin_manual/installation/docker/). If you're planning to use OwnCloud seriously, I highly recommend that because what I've showcased is merely a playground setup.

## Kasm Workspaces: Disposable Virtual Machines

If privacy and security are top priorities, then Kasm Workspaces is invaluable. It allows you to spin up an entire operating system within seconds. Some of you may be familiar with services that enable users to create disposable virtual machines on the cloud. Once you're finished with them, simply delete them.

### Especially Helpful

1. Running untrusted files securely.
    
2. Testing software on various Linux variants safely.
    
3. Avoiding fingerprinting on the internet effectively.
    

%[https://youtu.be/1mb835Qsx-8?si=J1TID4a0cgf_vohF&t=5] 

### Running Kali-Linux w/ GUI

```bash
sudo docker run --rm -it --shm-size=512m -p 6901:6901 -e VNC_PW=password kasmweb/core-kali-rolling:1.14.0
```

Above command will start a server on `0.0.0.0:6901`, now you can access Kali-Linux from browser via `https://0.0.0.0:6901`.

**Default Credentials**

* Username: kasm\_user
    
* Password: password
    

You can find more images under [Kasm Technologies](https://hub.docker.com/u/kasmweb) official DockerHub account.

<div data-node-type="callout">
<div data-node-type="callout-emoji">🔻</div>
<div data-node-type="callout-text">Kasm container at least require 10gb of storage.</div>
</div>

# Practical Lab: Understanding Network Protocols with Docker

Docker can be leveraged to clarify IT concepts such as networking. If you're interested in understanding "how do network protocols work?" then the combination of Docker, Scapy, and Wireshark is the optimal approach. Sometimes, I'm inclined to think that teaching network protocols without these tools should be prohibited.

Let's illustrate with an example. Suppose I want to understand "how would a machine react if I send random SYN/ACK packets?"

**Step 1:** Create two docker containers `attacker` and `victim` under same network.

```bash
docker network create mynetwork && \
docker run -d --network mynetwork --name attacker kalilinux/kali-rolling && \
docker run -d --network mynetwork --name victim kalilinux/kali-rolling
```

**Step 2:** Install `scapy` in attacker's machine. Write a script to send *SYN/ACK* packets (just google).

Scapy is a robust packet manipulation tool that enables users to craft, manipulate, send, and capture network packets across various layers of the OSI model. With Scapy, you can generate customized packets for network testing, analysis, and penetration testing.

**Step 3:** Capture traffic using Wireshark on the `mynetwork` interface. This will help filter out any unnecessary packets in the network.

# Portainer: Ultimate Toolbox for Container Management

Portainer is an open-source toolset that enables users to effortlessly build and manage containers across Docker, Docker Swarm, Kubernetes, and Azure ACI. It doesn't take long to realize that handling numerous containers can be daunting, but Portainer's Web UI streamlines the process. Users can easily manage containers, stacks, networks, and more with just a few clicks.

**Step 1:** First, create the volume for Portainer to store its database.

```bash
docker volume create portainer_data
```

**Step 2:** Pull and install the Portainer Server container.

```bash
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

**Step 3:** Visit `http://localhost:9443` in browser and start using it.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1714882444532/0c0e65b5-ffbe-4f01-9be6-9915429559ce.png align="center")

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">After the initial setup, such as creating an admin account, restart Portainer if it doesn't function correctly.</div>
</div>

# Conclusion

Docker is an invaluable skill set to possess, especially for distributing software packages. The software we discussed can be installed directly into your system, but the effort and maintenance required are significantly higher in comparison to Docker.

Personally, I use all the tools discussed above as my daily drivers, and of course, there are many other amazing things you can do with Docker. Just experiment, like learning about network protocols. If you have any amazing software, experiments, or anything else to share, feel free to do so in the comment section.

That's It For Today, See Yaa!