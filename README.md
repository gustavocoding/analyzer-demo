 Custom Analyzer demo
--------------------

 This sample project shows how to add a new analyzer to JDG 7.3.x and use it in remote queries over REST. The same queries should also work on Hot Rod.


## Preparation:

* To build the jar with the custom analyzer, execute:

```mvn clean install -s settings.xml```
    
* Copy the ```target/analyzer.jar``` to the ```standalone/deployments``` folder in the server

* Copy the ```standalone.xml``` file to the```standalone/configuration/``` folder in the server. This file defines an indexed cache named ```default``` and contains a reference to the analyzer jar.

* Start the server with ```bin/standalone.sh```

* Add a user/pass pair to the server with ```bin/add-user.sh -u user -p pass -a```

## Define the schema

The project contains a simple schema that makes use of the custom analyzer, named ```whitespaceLower``` . To register the schema, do via REST:

```
curl -u user:pass -X POST --data-binary @./schema.proto http://127.0.0.1:8080/rest/___protobuf_metadata/schema.proto
```

## Add some data

Add a few entries to the server: this can be done via REST as well:

```
curl -u user:pass -XPOST -d '{"_type": "User","description":"user@domain.com"}' -H "Content-Type: application/json" http://127.0.0.1:8080/rest/default/1
curl -u user:pass -XPOST -d '{"_type": "User","description":"CP-00001"}' -H "Content-Type: application/json" http://127.0.0.1:8080/rest/default/2
curl -u user:pass -XPOST -d '{"_type": "User","description":"Case Sensitive Content"}' -H "Content-Type: application/json" http://127.0.0.1:8080/rest/default/3
```

## Execute some queries

Queries can be executed in the browser by changing the ```query``` request params from

http://127.0.0.1:8080/rest/default?action=search&query=

Some examples:

* Retrieve all entities:

```query=from User```  

* Retrieve entities with full text predicate matching special characters:

```query=from User where description:'user@d*'``` 

* Case insensitive match including special characters. Note that the query always should be done with lowercase

```from User where description: 'cp-*'```

* Case insensitive query without wildcards. In this case the query can be done in upper and lower case

```from User where description: 'sensitive'```
```from User where description: 'Sensitive'```



