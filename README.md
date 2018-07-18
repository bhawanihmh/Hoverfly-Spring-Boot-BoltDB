# SpringBootHoverflyBoltDB
Implementations of Hoverfly proxy in Spring Boot using Bolt db. 

## Install Hoverfly on MacOS
brew install SpectoLabs/tap/hoverfly
More : http://hoverfly.readthedocs.io/en/latest/pages/introduction/downloadinstallation.html

## Install Bolt DB
go get github.com/boltdb/bolt/...
More :  https://github.com/boltdb/boltd

## Hoverfly commands
hoverfly -ap 8880 -pp 8555 -listen-on-host 192.168.9.153 -db "boltdb" -db-path ~/sample.db

INFO[2018-07-18T15:24:22+05:30] Default proxy port has been overwritten       port=8555
INFO[2018-07-18T15:24:22+05:30] Default admin port has been overwritten       port=8880
INFO[2018-07-18T15:24:22+05:30] Listen on specific interface                  host=192.168.9.153
INFO[2018-07-18T15:24:22+05:30] Initiating database                           databaseName=/Users/bhawani.s.shekhawat/sample.db
INFO[2018-07-18T15:24:22+05:30] Using boltdb backend                         
INFO[2018-07-18T15:24:22+05:30] Proxy prepared...                             Destination=. Mode=simulate ProxyPort=8555
INFO[2018-07-18T15:24:22+05:30] current proxy configuration                   destination=. mode=simulate port=8555
INFO[2018-07-18T15:24:22+05:30] Admin interface is starting...                AdminPort=8880
INFO[2018-07-18T15:24:22+05:30] serving proxy 

## SpringBoot changes:
RestTemplate restTemplate = new RestTemplate();
SimpleClientHttpRequestFactory requestFactory = new SimpleClientHttpRequestFactory();
Proxy proxy = new Proxy(Type.HTTP, new InetSocketAddress("192.168.9.153", 8555));
requestFactory.setProxy(proxy);
restTemplate =  new RestTemplate(requestFactory);
Programme programme = restTemplate.getForObject("http://127.0.0.1:8091/programme/{id}", Programme.class, programId);
