# Getting Started - Update your first pod

## Dockerfile V2

This is the Dockerfile we will use:

```dockerfiles
from nginx:alpine
COPY files/index.html /usr/share/nginx/html/index.html
```

I created a folder named `files` but you could **remove it from the path** and simply write this instead:

`COPY index.html /usr/share/nginx/html/index.html`

## Let's get your hands dirty a little

We made the first example working using deprecated cli commands ( Talking about the run command in particular). I had to remove the pod and recreate one. I belive it would simpler to create a yml file and apply it. Let's get started! 

You should start by removing what's you've got so far:

```bash
kubectl delete pod hello
kubectl delete service/hello
```

[You can use this yaml file](deployments/v2_deployment_hello.yaml)

Then you are required to run the yaml file using `apply` command.

```bash
kubectl apply -f deployments/v2_deployment_hello.yaml
kubectl expose deployment/hello --port=80 --type="NodePort" --name=hello
```