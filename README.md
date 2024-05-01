# nginx-hello-world
This is a simple nginx example with an Openshift ( Tekton) pipeline with integration into Advanced Cluster Security for image scans and security policy checks.

See the ACS documentation here https://docs.openshift.com/acs/4.4/welcome/index.html

This link will show you how to generate the API token for access to the roxcli scanning tool. You need to update the roxsecrets secret in the manifests folder with the api token and api url for your openshift cluster that has the advanced cluster security operator deployed.
https://docs.openshift.com/acs/4.4/cli/using-the-roxctl-cli.html#create-api-token_using-roxctl-cli

Note: if you update the rox-secret.yaml directly you will need to base64 encode the api token and url like this:

```
echo '<My Token>' | base64 
```

Otherwise just edit it via teh secret in the Console under workloads->Secrets.

# import a mimnimal ubi image to openshift and store it in the local registry

You can import the base images into the local registry if desired for faster load times.Make sure they are imported into teh openshift namesapce so they can be pulled from all namespaces.

In the docker file you need to reference the local registry image instead of the Red Hat Registry.


```
    # Use Red Hat Universal Base Image 9 Minimal
      #FROM registry.access.redhat.com/ubi9/ubi-minimal:latest
      FROM image-registry.openshift-image-registry.svc:5000/openshift/ubi9-minimal:latest
```

## examples of importing images from Red Hat repos
```
oc import-image ubi9/ubi-micro:latest --scheduled=true --from=registry.access.redhat.com/ubi9/ubi-micro:latest --confirm -n openshift

oc import-image ubi9-minimal:9.4-949 --scheduled=true --from=registry.access.redhat.com/ubi9-minimal:9.4-949 --confirm -n openshift

oc import-image ubi9-minimal:latest --scheduled=true --from=registry.access.redhat.com/ubi9-minimal:latest --confirm -n openshift
```

# nginx conf

Note: The root is set to /usr/share/nginx/html in the dockerfile so make sure your nginx.conf uses the below root:

```
 root /usr/share/nginx/html;
```

Full nginx.conf example

```
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /tmp/nginx.pid;  # Changed to a writable directory for the non-root user

events {
    worker_connections 1024;
}

http {
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    access_log /var/log/nginx/access.log ;

    server {
        listen 8080;
        server_name localhost;
        
        root /usr/share/nginx/html;
        index index.html index.htm;

        location / {
            try_files $uri $uri/ =404;
        }
        
        server_tokens off;
    }
}
```