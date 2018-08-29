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

**hoverfly -ap 8880 -pp 8555 -listen-on-host XXX.XXX.XXX.XXX -db "boltdb" -db-path ~/sample.db** <br />

INFO[2018-07-18T15:24:22+05:30] Default proxy port has been overwritten       port=8555<br />
INFO[2018-07-18T15:24:22+05:30] Default admin port has been overwritten       port=8880<br />
INFO[2018-07-18T15:24:22+05:30] Listen on specific interface                  host=XXX.XXX.XXX.XXX<br />
INFO[2018-07-18T15:24:22+05:30] Initiating database                           databaseName=/Users/bhawani.s.shekhawat/sample.db<br />
INFO[2018-07-18T15:24:22+05:30] Using boltdb backend <br />                        
INFO[2018-07-18T15:24:22+05:30] Proxy prepared...                             Destination=. Mode=simulate ProxyPort=8555<br />
INFO[2018-07-18T15:24:22+05:30] current proxy configuration                   destination=. mode=simulate port=8555<br />
INFO[2018-07-18T15:24:22+05:30] Admin interface is starting...                AdminPort=8880<br />
INFO[2018-07-18T15:24:22+05:30] serving proxy <br />

## SpringBoot changes:

import org.springframework.http.client.SimpleClientHttpRequestFactory;<br />
import java.net.InetSocketAddress;<br />
import java.net.Proxy;<br />
import java.net.Proxy.Type;<br />

RestTemplate restTemplate = new RestTemplate();<br />
SimpleClientHttpRequestFactory requestFactory = new SimpleClientHttpRequestFactory();<br />
Proxy proxy = new Proxy(Type.HTTP, new InetSocketAddress("XXX.XXX.XXX.XXX", 8555));<br />
requestFactory.setProxy(proxy);<br />
restTemplate =  new RestTemplate(requestFactory);<br />
Programme programme = restTemplate.getForObject("http://127.0.0.1:8091/programme/{id}", Programme.class, programId);<br />

## More about Hoverfly:<br />
https://hoverfly.readthedocs.io/en/latest/pages/tutorials/advanced/advanced.html <br />

## Using middleware:<br />
Hoverfly middleware can be written in any language. Middleware modules receive a service data JSON string via the standard input (STDIN) and must return a service data JSON string to the standard output (STDOUT).<br />
To implement more dynamic behaviour, middleware should be combined with Hoverfly's synthesize mode (see the Creating synthetic services section).<br/>
### Javascript example<br />
This example will change the response code and body in each response.<br/>
Ensure that you have captured some traffic with Hoverfly<br/>
Ensure that you have NodeJS installed<br/>
Save the following code into a file named example.js and make it executable (chmod +x example.js):<br/>
 #!/usr/bin/env node<br/>

 process.stdin.resume(); <br/> 
 process.stdin.setEncoding('utf8');<br/>  
 process.stdin.on('data', function(data) {<br/>
   var parsed_json = JSON.parse(data);<br/>
   // changing response<br/>
   parsed_json.response.status = 201;<br/>
   parsed_json.response.body = "body was replaced by JavaScript middleware\n";<br/>
<br/>
   // stringifying JSON response<br/>
   var newJsonString = JSON.stringify(parsed_json);<br/>
<br/>
   process.stdout.write(newJsonString);<br/>
 });<br/>
Restart Hoverfly in simulate mode with the example.js script specified as middleware:<br/>
   ./hoverfly -middleware "./example.js"<br/>
Repeat the steps you took to capture the traffic You will notice that every response will have the 201 status code,<br/> and the body will have been replaced by the string specified in the script.<br/>
<br/>
