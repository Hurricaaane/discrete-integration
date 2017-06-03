The following are personal notes on how to install a private Docker registry.

All instructions are for **hobbyists**. Use at own risk.

## Description

Private Docker registry that is run within Docker.

Assuming Docker is already installed.

This is a hashed version of docker documentation: https://docs.docker.com/registry/deploying/#restricting-access

TLS certificates must be available for the domain, therefore a domain
and its TLS certificates (generated through Let's Encrypt for instance) are required.

## Volume

Three disk based volume will be used here, represented by:
- `/data/docker-data/registry/certs`
- `/data/docker-data/registry/auth`
- `/data/docker-data/registry/rdata`

## Install the certificates

Copy the certificates:
- from `.../certs/live/.../fullchain.pem` to `/data/docker-data/registry/certs/domain.crt`
- from `.../certs/live/.../privkey.pem` to `/data/docker-data/registry/certs/domain.key`

## Create a password

Create new credentials, let `$USER` be the user, and `$PASS` be the password.

Run `docker run --entrypoint htpasswd registry:2 -Bbn $USER $PASS > /data/docker-data/registry/auth/htpasswd`

Remove the newly created container.

## Create the container

    docker create \
        --name registry \
        --restart=unless-stopped \
        --volume /data/docker-data/registry/rdata:/rdata \
        -e REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY=/rdata \
        -v /data/docker-data/registry/certs:/certs \
        -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
        -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
        -v /data/docker-data/registry/auth:/auth \
        -e "REGISTRY_AUTH=htpasswd" \
        -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
        -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
        -p 5000:5000 \
        registry:2
        
## Start the container

    docker start registry
    
## Test

Attempt login onto the registry. Prefer using another server.
    
    docker login -u $USER -p $PASS example.com:5000
