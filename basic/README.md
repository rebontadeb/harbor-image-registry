# 1. Install Docker

```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
systemctl start docker
systemctl enable docker
```
# 2. Install Docker Compose
```
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v2.23.0/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
cp $DOCKER_CONFIG/cli-plugins/docker-compose /usr/local/bin/docker-compose
```

# 3. Download Harbor for Offline Installation.
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

# 4. Configure Harbor using `harbor.yml`
Harbor needs a configuration file which is used to prepare the installation.
Refference YML file can be found [here](../artifacts/basic-harbor.yml)

# 5. Configure HTTPS

**Note: Harbor can be configured with IP Address/Hostname/DNS. In this example IP Address is used**

1. Create CA certificate and key.
```
openssl genrsa -out ca.key 4096

openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=CN/ST=India/L=India/O=rdeb/OU=Personal/CN=45.79.121.182" \
 -key ca.key \
 -out ca.crt
```
2. Create Server Certificate
```
openssl genrsa -out harbor.key 4096

openssl req -sha512 -new \
-subj "/C=CN/ST=India/L=India/O=rdeb/OU=Personal/CN=45.79.121.182" \
-key harbor.key \
-out harbor.csr

cat > v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names
[alt_names]
DNS.1=45.79.121.182
DNS.2=45.79.121.182
DNS.3=45.79.121.182
EOF

openssl x509 -req -sha512 -days 3650 \
-extfile v3.ext \
-CA ca.crt -CAkey ca.key -CAcreateserial \
-in harbor.csr \
-out harbor.crt
```
3. Copy the Certificates in respective directories.
```
mkdir -p /data/cert/
cp harbor.crt /data/cert/
cp harbor.key /data/cert/
openssl x509 -inform PEM -in harbor.crt -out harbor.cert
mkdir -p /etc/docker/certs.d/45.79.121.182
cp harbor.cert /etc/docker/certs.d/45.79.121.182/
cp harbor.key /etc/docker/certs.d/45.79.121.182/
cp ca.crt /etc/docker/certs.d/45.79.121.182/
```
4. Restart Docker for the certificates to take effect.
```
systemctl restart docker
```
# 6. Install Harbor
Go inside the un-archived directory of harbor and execute the following commands one after other.
```
./prepare
./install.sh --with-trivy
```
