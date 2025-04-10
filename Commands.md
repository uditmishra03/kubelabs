### 1. Create a service redis-service to expose the redis application within the cluster on port 6379.

``` kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml ```

### 2. Create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas.

``` kubectl create deploy webapp --image=kodekloud/webapp-color --replicas=3 ```

### 3. Create a new pod called custom-nginx using the nginx image and run it on container port 8080.

``` kubectl run custom-nginx --image=nginx --port=8080 ```

### 4. Create a new namespace called dev-ns.

``` kubectl create ns dev-ns ```
