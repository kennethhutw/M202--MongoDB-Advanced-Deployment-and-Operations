Question 8: Traffic Imbalance in a Sharded Environment\
------- 

$ mongo --nodb
> config = { d0 : { smallfiles : "", noprealloc : "", nopreallocj : ""}, d1 : { smallfiles : "", noprealloc : "", nopreallocj : "" } };
> cluster = new ShardingTest( { shards: config } );

Once the cluster is up, click "Initialize" in MongoProc one time to finish setting up the cluster's data and configuration.
It will take a while to finish. 

after finish the initialize, you go to mongos

"mongo --port 30999"

The question si chunks too much in mongodb so you have to move some to other server. 

how to check how many chunks in mongodb. 
there are two ways.
PS switch to config database first
1. >sh.status() 
2. >db.chunks.find().count()  


you need to knwo what the name of server of mongo is. 
```javascript
>first_doc = db.chunks.find().next()
```
You look at id -- m202.imbalance

and then move chunk
```javascript
>sh.stopBalancer()
> use admin
>db.runCommand( { moveChunk : "m202.imbalance", bounds : [{"_id": ISODate("2014-07-16T00:00:00Z")},{"_id": ISODate("2014-07-17T00:00:00Z")}],"to":"shard0001"})
>db.runCommand( { moveChunk : "m202.imbalance", bounds : [{"_id": ISODate("2014-07-17T00:00:00Z")},{"_id": ISODate("2014-07-18T00:00:00Z")}],"to":"shard0001"})
```
