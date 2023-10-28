# Install Docker

```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
systemctl start docker
systemctl enable docker
```
# Install Docker Compose
```
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v2.23.0/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
cp $DOCKER_CONFIG/cli-plugins/docker-compose /usr/local/bin/docker-compose
```

# Download Harbor for Offline Installation.
You Can Reach to [harbor download page](https://github.com/goharbor/harbor/releases) for latest versions 

1. Download a specific version.
```
wget https://github.com/goharbor/harbor/releases/download/v2.9.0/harbor-offline-installer-v2.9.0.tgz
wget https://github.com/goharbor/harbor/releases/download/v2.9.0/harbor-offline-installer-v2.9.0.tgz.asc
```
2. Download a gpg keys to ensure the package is authentic.
```
gpg --keyserver hkps://keyserver.ubuntu.com --receive-keys 644FF454C0B4115C
gpg -v --keyserver hkps://keyserver.ubuntu.com --verify harbor-offline-installer-v2.9.0.tgz.asc

```
3. Untar the Package 

`tar xzvf harbor-offline-installer-v2.9.0.tgz`

# Configure Harbor using harbor.yml
Harbor needs a configuration file which is used to prepare the installation.
Refference YML file can be found [here](./artifacts/basic-harbor.yml)

# Configure HTTPS