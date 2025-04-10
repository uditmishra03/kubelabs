### 1. Create a service redis-service to expose the redis application within the cluster on port 6379.

``` kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml ```

### 2. Create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas.

``` kubectl create deploy webapp --image=kodekloud/webapp-color --replicas=3 ```

### 3. Create a new pod called custom-nginx using the nginx image and run it on container port 8080.

``` kubectl run custom-nginx --image=nginx --port=8080 ```

### 4. Create a new namespace called dev-ns.

``` kubectl create ns dev-ns ```

### 5. Create a new deployment called redis-deploy in the dev-ns namespace with the redis image. It should have 2 replicas.

``` kubectl create deploy redis-deploy --image=redis --replicas=2 -n dev-ns ```

### 6. Create a pod called httpd using the image httpd:alpine in the default namespace. Next, create a service of type ClusterIP by the same name (httpd). The target port for the service should be 80.

``` kubectl run httpd --image=httpd:alpine ```
``` kubectl expose pod httpd --port=80 --name httpd --type=ClusterIP ```
