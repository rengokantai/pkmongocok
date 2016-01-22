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
```
