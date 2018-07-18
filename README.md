# SpringBoot Hoverfly BoltDB
Implementations of Hoverfly proxy in Spring Boot using Bolt DB. <br />

## Install Hoverfly on MacOS

**brew install SpectoLabs/tap/hoverfly** <br />
More : http://hoverfly.readthedocs.io/en/latest/pages/introduction/downloadinstallation.html<br />

## Install Bolt DB

**go get github.com/boltdb/bolt/...** <br />
More :  https://github.com/boltdb/boltd<br />

## Hoverfly commands

**Hoverfly behind a proxy**<br />
hoverctl start --upstream-proxy http://corp.proxy:8080<br />

**Upstream proxy authentication**<br />
hoverctl start --upstream-proxy http://my-user:my-pass@corp.proxy:8080<br />

**Controlling a remote Hoverfly instance with hoverctl**<br />

hoverfly -ap 8880 -pp 8555<br />

On your local machine, you can create a target named remote using hoverctl. This target will be configured to communicate with Hoverfly.<br />
hoverctl targets create remote \
    --host hoverfly.example.com \
    --admin-port 8880 \
    --proxy-port 8555<br />
    
Now that hoverctl knows the location of the remote Hoverfly instance, run the following commands on your local machine to capture and simulate a URL using this instance:<br />

hoverctl -t remote mode capture<br />
curl --proxy http://hoverfly.example.com:8555 http://ip.jsontest.com<br />
hoverctl -t remote mode simulate<br />
curl --proxy http://hoverfly.example.com:8555 http://ip.jsontest.com<br />

You will now need to specify the remote target every time you want to interact with this Hoverfly instance. If you are only working with this remote instance, you can set it to be the default target instance for hoverctl.<br />

hoverctl targets default remote<br />

How can I view the Hoverfly logs?<br />
hoverctl logs

**Why am I not able to access my Hoverfly remotely?**
That’s because Hoverfly is bind to loopback interface by default, meaning that you can only access to it on localhost. To access it remotely, you can specify the IP address it listens on. For example, setting 0.0.0.0 to listen on all network interfaces.<br />
hoverfly -listen-on-host 0.0.0.0 <br />

**hoverfly -ap 8880 -pp 8555 -listen-on-host 192.168.9.153 -db "boltdb" -db-path ~/sample.db** <br />

INFO[2018-07-18T15:24:22+05:30] Default proxy port has been overwritten       port=8555<br />
INFO[2018-07-18T15:24:22+05:30] Default admin port has been overwritten       port=8880<br />
INFO[2018-07-18T15:24:22+05:30] Listen on specific interface                  host=192.168.9.153<br />
INFO[2018-07-18T15:24:22+05:30] Initiating database                           databaseName=/Users/bhawani.s.shekhawat/sample.db<br />
INFO[2018-07-18T15:24:22+05:30] Using boltdb backend <br />                        
INFO[2018-07-18T15:24:22+05:30] Proxy prepared...                             Destination=. Mode=simulate ProxyPort=8555<br />
INFO[2018-07-18T15:24:22+05:30] current proxy configuration                   destination=. mode=simulate port=8555<br />
INFO[2018-07-18T15:24:22+05:30] Admin interface is starting...                AdminPort=8880<br />
INFO[2018-07-18T15:24:22+05:30] serving proxy <br />

## SpringBoot changes:

RestTemplate restTemplate = new RestTemplate();<br />
SimpleClientHttpRequestFactory requestFactory = new SimpleClientHttpRequestFactory();<br />
Proxy proxy = new Proxy(Type.HTTP, new InetSocketAddress("192.168.9.153", 8555));<br />
requestFactory.setProxy(proxy);<br />
restTemplate =  new RestTemplate(requestFactory);<br />
Programme programme = restTemplate.getForObject("http://127.0.0.1:8091/programme/{id}", Programme.class, programId);<br />

## More about Hoverfly:<br />
https://hoverfly.readthedocs.io/en/latest/pages/tutorials/advanced/advanced.html

