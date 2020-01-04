# Getting Started - Update your first pod

## Dockerfile V2

This is the Dockerfile we will use:

```dockerfiles
from nginx:alpine
COPY files/index.html /usr/share/nginx/html/index.html
```

I created a folder named `files` but you could **remove it from the path** and simply write this instead:

`COPY index.html /usr/share/nginx/html/index.html` 
