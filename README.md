# Running Couchbase cluster and sync gateway with Docker
## Create Docker network
```
docker network create --driver bridge couchbase
```
## Couchbase docker
https://hub.docker.com/_/couchbase

Single node cluster joined to couchbase network:
```
docker pull couchbase:enterprise-6.0.3
docker run -d --net=couchbase --name couchbase-server -p 8091-8094:8091-8094 -p 11210:11210 -v /tmp/couchbase:/opt/couchbase/var couchbase:enterprise-6.0.3
```
http://localhost:8091

Create new cluster:
Name: couchbase-server
Admin Username: Administrator
Password: password

> Note. Please use a more secure admin user name and password for a cluster that is accessible beyond of Localhost  

Add a bucket called "default" before starting the sync gateway.

## Sync Gateway docker 
https://hub.docker.com/r/couchbase/sync-gateway

```
docker pull couchbase/sync-gateway:2.6.1-enterprise
mv sg-config.json /tmp/sg-config.json
docker run --net=couchbase --name couchbase-sgw -p 4984:4984 -v /tmp:/tmp/config -d couchbase/sync-gateway:2.6.1-enterprise /tmp/config/sg-config.json
```
Check that the gw is running:
```
curl http://localhost:4984
# {"couchdb":"Welcome","vendor":{"name":"Couchbase Sync Gateway","version":"2.6"},"version":"Couchbase Sync Gateway/2.6.1(1;4907fed) EE"}
```
By default the sw admin port is only accessible from container's localhost:
```
docker exec -ti sgw bash
# curl http://localhost:4985
# {"ADMIN":true,"couchdb":"Welcome","vendor":{"name":"Couchbase Sync Gateway","version":"2.6"},"version":"Couchbase Sync Gateway/2.6.1(1;4907fed) EE"}

