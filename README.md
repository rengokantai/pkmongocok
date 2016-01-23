#### pkmongocok

- cp1
starting multi ins as a part of replset. 
open three shells.
Need to create three folders and log folder, and assign port to 27000,27001,27002
(the -fork is only suitable for *nix os)
```
mongod --replSet replSet --port 27000 --dbpath mongodata1 --logpath logs\1.log --smallfiles --oplogSize 128
mongod --replSet replSet --port 27001 --dbpath mongodata2 --logpath logs\2.log --smallfiles --oplogSize 128
mongod --replSet replSet --port 27002 --dbpath mongodata3 --logpath logs\3.log --smallfiles --oplogSize 128
```
Open the fourth shell
```
mongo localhost:27000
```
Then
```
cfg={'_id':'replSet','members':[{'_id':0,'host':'localhost:27000'},{'_id':1,'host':'localhost:27001'},{'_id':2,'host':'localhost:27002'}]}
```
Then initialize
```
rs.initialize(cfg)
rs.status()
```

slaveOk
If you insert a record in primary set, if you connect to secondary set, it will be an error.
must set:
```
rs.slaveOk(true)
//Or rs.slaveOk()
```
Even enabled slaveOk, secondary set can not add records. Only read operations are available.
```
db.shutdownServer()
```

create sharded environment
```
mongod --shardsvr --dbpath mongoshrdata1 --port 27000 --logpath logs/1.log --smallfiles --oplogSize 128 (-fork)
mongod --shardsvr --dbpath mongoshrdata1 --port 27001 --logpath logs/2.log --smallfiles --oplogSize 128 (-fork)
mongod --configsvr --dbpath mongoshrcfg --port 25000 --logpath logs/cfg.log (-fork)
mongos --configdb localhost:25000 --logpath logs/mongos.log (-fork)
```
Then run
```
mongo
sh.addShard("localhost:27000")
sh.addShard("localhost:27001")
```
connect to a shard
```
use testDB
sh.enableSharding("testDB")
```
shard a collection
```
sh.shardCollection("testDB.person",{name:"hashed"},false}
```


- cp5
automic operation
```
db.ec2Test.findAndModify({query:{_id:1},update:{$set:{name:"Ekk"}},fields:{_id:0,name:1},new:false})
```
possible params:
upsert,new(if set to true, return modified result, although already modified)


- cp7
deploy mongodb on EC2
Ubuntu 14.04:
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee/etc/apt/sources.list.d/mongodb-org-3.2.list
sudo apt-get update
sudo apt-get install -y mongodb-org
sudo service mongod start
```
Start:
```
sudo mkdir logs data
sudo mongod --dbpath data --logpath log/mongo.log --smallfiles --oplogSize 50 -fork
mongo
```

deploy on docker:
```
wget -qO- https://get.docker.com/ |sh
sudo service docker start         //wait 10 secs
sudo docker info   //must use sudo
sudo docker info
sudo docker pull mongo
sudo docker images | grep mongo
sudo docker run -d --name mongoserver1 mongo
sudo docker inspect mongoserver1 |grep IPAddress  //get ip
```
connect:
```
mongo <ip>
```
craete a container
```
mkdir -p /data/db1
sudo docker run -d --name server2 -v /data/db1:/data/db mongo
```
create another container
```
mkdir -p /data/db3
sudo docker run -d --name server3 -v /data/db3:/data -p 9999:27017 mongo
```
connect:
```
mongo localhost:9999
```

