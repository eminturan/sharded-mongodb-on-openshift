# sharded-mongodb-on-openshift

## Steps

#### 1. Deploy Shards 

```
oc apply -f shard.yml
oc apply -f shard2.yml
```

#### 2. Initiate shards

Open one of the shard's pod's terminal, then

```
mongo --port 27018

cfg= {_id: "shard0", members: [
     {_id: 0, host: "mongodb-shard1-0.mongodb-shard1-headless-service.namespace.svc.cluster.local:27018"},
     {_id: 1, host: "mongodb-shard2-0.mongodb-shard2-headless-service.namespace.svc.cluster.local:27018"}
], settings: {electionTimeoutMillis: 2000}};

rs.initiate(cfg);
```

#### 3. Deploy Config 

```
oc apply -f config.yml

```

#### 4. Initiate Config

Open the config's pod's terminal, then

```
mongo --port 27018

cfg= {_id: "configdb", members: [
     {_id: 0, host: "mongodb-configdb-0.mongodb-configdb-headless-service.namespace.svc.cluster.local:27018"}
], settings: {electionTimeoutMillis: 2000}};

rs.initiate(cfg);
```

#### 5. Deploy Mongos

```
oc apply -f mongos.yml
```

#### 6. Add Shards

```
mongo --port 27018

sh.addShard("shard0/mongodb-shard1-0.mongodb-shard1-headless-service.namespace.svc.cluster.local:27018,mongodb-shard2-0.mongodb-shard2-headless-service.namespace.svc.cluster.local:27018");

```

#### 7. Check Status

```
sh.status();
```






