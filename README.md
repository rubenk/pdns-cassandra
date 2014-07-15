pdns-cassandra
==============

Remote backend backend for PowerDNS that talks to Cassandra

Getting started
--------------

Create Cassandra schema with cqlsh (tested with Cassandra 2.0.8):

    CREATE KEYSPACE dns WITH replication = {
        'class': 'SimpleStrategy',
        'replication_factor': '3'
    };

    USE dns;

    CREATE TABLE domain_metadata (
        name ascii,
        kind ascii,
        content ascii,
        PRIMARY KEY (name, kind)
    );

    CREATE TABLE domains (
        zone ascii,
        kind ascii,
        masters list<ascii>,
        PRIMARY KEY (zone)
    );

    CREATE TABLE records (
        qname ascii,
        qtype ascii,
        content ascii,
        priority int,
        ttl int,
        PRIMARY KEY (qname, qtype, content)
    );

    CREATE TABLE supermasters (
        nameserver ascii,
        ip ascii,
        account ascii,
        PRIMARY KEY (nameserver, ip)
    );

Make sure you have the Cassandra driver from Datastax installed:
<https://github.com/datastax/python-driver>

The recommended way is to just:

    $ pip install cassandra-driver
    
Configure the PowerDNS remote backend:

    launch=remote
    remote-connection-string=http:url=http://localhost:5000
    
Now start the application:

    DEBUG=True KEYSPACE=dns CASSANDRA_NODES=10.0.0.1,10.0.0.2 python pdns_cassandra.py
