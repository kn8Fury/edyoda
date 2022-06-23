## Write a common use-case, where you will use a daemon set instead of replica set.

1. Infra-health monitoring applications.  
   In this case we want only one pod on one node to prevent
   - collecting duplicate data for some nodes and
   - failing to collect data for some nodes
2. Load-balancing applications.  
   Similarly in this case, we want to have the load metrics for each node
