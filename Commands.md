### Create a service redis-service to expose the redis application within the cluster on port 6379.

kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
