# NANOMQ setup
## Installation
Install with Docker
```bash
docker pull emqx/nanomq:latest
```
Install with package (Linux)
```bash
wget https://www.emqx.com/en/downloads/nanomq/<version>/nanomq-<version>-linux-x86_64.rpm
# wget https://www.emqx.com/en/downloads/nanomq/0.24.10/nanomq-0.24.10-linux-x86_64.rpm

sudo rpm -ivh nanomq-<version>-linux-x86_64.rpm
# sudo rpm -ivh nanomq-0.24.10-linux-x86_64.rpm

nanomq start
```
