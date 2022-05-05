# Deploy-Redis-on-EKS

**REDIS**
Redis is an open source, in-memory data structure store, used as a database(No SQL), cache, and message broker. Redis provides data structures such as strings, hashes, lists, sets, sorted sets with range queries, bitmaps, hyperloglogs, geospatial indexes, and streams. It store datas as 'key-value' pair.

You can use Redis from most programming languages. Redis can be used with streaming solutions such as Apache Kafka and Amazon Kinesis as an in-memory data store to ingest, process, and analyze real-time data with sub-millisecond latency. Redis is an ideal choice for real-time analytics use cases such as social media analytics, ad targeting, personalization, and IoT. 

**Step 1: Connect to Created Cluster**

```
$ aws configure
$ aws sts get-caller-identity (to get User Id and Account number)
$ aws eks --region region update-kubeconfig --name cluster_name
```

**Step 2: Clone Repository**

`$ git clone https://github.com/Aswathir26/Deploy-Redis-on-EKS.git`

**Step 3: Create Namespace and storage class**

`$ kubectl create ns redis`
`$ kubectl apply -f redis-sc.yaml`

**Step 4: Add Bitnami Repo and Install Redis chart**

```
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm install -f values.yaml redis bitnami/redis --namespace redis
```

You will get Password for redis by export and echo commands.

Now you can check the pods created

`$ kubectl get pods`

You can see there are One master pod and three slave pods running.

**Test Replication/Connection between master and slaves**

You have successfully created the Redis cluster. Now it is time to test the replication. Note that you are able to write data on the master pod only; slave pods are used for reading purposes only. So, log in to the master pod and create some key-value pair data. Then check if the same data is replicated in the slave pods.

 enter into the master pod:

    $ kubectl exec -it redis-master-0 -- sh

Connect to Redis using the Redis CLI:

    $ redis-cli 

Authenticate yourself using the master password:

    > auth <password>

You’re authenticated with the Redis master instance, but as of now, you don’t have any data. Create some key-value pair data using the following command:

    > SET emp1 ammu
    > SET emp2 pranav
    > SET emp3 aswathi

Now get the key-value pair list:

    > KEYS *
    
    Now log in to the slave pods and check to see if they show the same three data.
Enter into the slave pod redis-replicas-0:

     $ kubectl exec -it redis-replicas-0 -- sh

Connect to Redis using the Redis CLI:


    $ redis-cli

Authenticate yourself using the slave password:

    > auth <password>

Get the key-value pair list:


    > KEYS *
    
    
This shows the same three data you created on the master pod. Repeat the same steps for slave 2 by changing the name to redis-replicas-1 in the exec command to enter into the pod and check the data.
