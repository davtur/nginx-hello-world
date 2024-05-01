# nginx-hello-world
Simple nginx example 


# import a micro image to openshift and store it in the local registry

oc import-image ubi9/ubi-micro:latest --scheduled=true --from=registry.access.redhat.com/ubi9/ubi-micro:latest --confirm -n openshift


oc import-image ubi9-minimal:9.4-949 --scheduled=true --from=registry.access.redhat.com/ubi9-minimal:9.4-949 --confirm -n openshift

oc import-image ubi9-minimal:latest --scheduled=true --from=registry.access.redhat.com/ubi9-minimal:latest --confirm -n openshift
