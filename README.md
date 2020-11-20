# Undertanding Docker ONBUILD 



## Folder Structure

```shell
.
├── README.md           # explain how to
├── httpd-parent        # create base image
│   ├── Dockerfile
│   └── openshift-build-and-deploy.sh
└── using_builder-httpd # use previous image
    ├── Dockerfile
    └── src
        └── index.html
```



# Docker

## First Create Builder

```shell
# build (doesn't require the index.html)
docker build --no-cache -t httpd-parent:latest httpd-parent/

# run
docker run --rm -it --name app -p 8001:80 httpd-parent:latest

# in another terminal
curl localhost:8001
## show oficial page
```

## Use the Builder

```shell
# build using previous (require the index.html)
docker build --no-cache -t app-only:v1 using_builder-httpd/

# run
docker run --rm -it --name app -p 8002:80 app-only:v1

# in another terminal
curl localhost:8002
## show custom page
```

# Openshift

### Parent

```shell
$ export USER_ID=$(oc whoami)
$ oc new-project ${USER_ID}-docker-hello

$ oc new-app --name httpd-parent https://github.com/jovemfelix/docker-demo-using-onbuild.git --context-dir=httpd-parent
```

> AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.2.20. Set the 'ServerName' directive globally to suppress this message
> (13)Permission denied: AH00072: make_sock: could not bind to address [::]:80
> (13)Permission denied: AH00072: make_sock: could not bind to address 0.0.0.0:80
> no listening sockets available, shutting down
> AH00015: Unable to open logs

### Child

```shell
$ oc new-app --name child https://github.com/jovemfelix/docker-demo-using-onbuild.git --context-dir=using_builder-httpd
```



# Reference

- https://www.learnitguide.net/2018/06/docker-onbuild-command-with-examples.html
- https://www.openshift.com/blog/create-s2i-builder-image
