# Service

## You have deployed an application, that is listening at port 8000. You used a replica-set to deploy it and created a NodePort service to make it accessible. But when you test it, somehow the application is not reachable at the port. Write down your approach and sequentially all the steps that you will take to find out the issue.

1. Confirm if 8000 is exposed in dockerfile
1. Check if app is running in container
1. Hit container through 8000 port
1. Hit pod through pod port
1. Hit service through service port
1. Hit the node through nodeport