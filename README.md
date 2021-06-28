# MongoDB to Elastic data exporter

Export data from mongo database to elasticsearch via kafka. This repository uses debezium mongo connector as source and confluent elasticsearch connector as sink for the data pipeline.

## Pre-requisite
 - You must also have a MongoDB user that has the appropriate roles to read the admin database where the oplog can be read. Additionally, the user must also be able to read the config database in the configuration server of a sharded cluster and must have listDatabases privilege action.
 - Elasticsearch assigned privileges: `create_index`, `create_doc`, `monitor`, `view_index_metadata`, and `write`

## Installation
Start mongodb, kafka, zookeeper, kafka connector, elastic search, kibana using docker-compose file:
```
docker-compose up
```

### Sample data to mongo

Load sample data in mongodb:
```
mongorestore  --db=golan --username=root --authenticationDatabase=admin --archive=../../data/test.20210627.archive --port=37017
```
 
### Register Connectors
 - Register mongo source connector: `curl -i -X POST -H "Accept:application/json" -H  "Content-Type:application/json" http://localhost:8083/connectors/ -d @register-mongodb.json`
 	- change `database.include.list` property to set database to export
 	- `transforms.unwrap.array.encoding` property can have array, document type. Array type assumes that all the fields in array will be of same type i.e. strict schema. If your usecase doesn't have strict schema, change it to `document`
 - Register elastic sink connector: `curl -i -X POST -H "Accept:application/json" -H  "Content-Type:application/json" http://localhost:8083/connectors/ -d @register-elastic.json`
 	- change `topics` in format <mongodb.name>.<database>.<collection>
 	- `schema.ignore` property to have dynamic schema
 	
### Elastic index modification
 - If you need to exclude any field from indexing in elastic search: `curl -i -X PUT -H "Accept:application/json" -H  "Content-Type:application/json" http://localhost:9200/mongodb.golan.documents/_mapping/ -d @elastic-index-change.json`
 - In this example, it un-index `PreviousVersions` field of `mongodb.golan.documents` index in elasticsearch.
 
## Extras
 - To delete a connector:
 ```
 curl -X DELETE http://localhost:8083/connectors/elastic-sink
 ```
