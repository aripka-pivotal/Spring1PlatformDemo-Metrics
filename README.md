# Spring1PlatformDemo-Metrics

Demo project used to demonstrate PCF metrics and New Relic integration.

Each submodule projects manifest-unversioned.yml file should be updated to change the host to be the space that the demo would be run in &lt;&lt;submodule&gt;&gt;-&lt;&lt;space&gt;&gt; e.g. quotes-s1p12

**Deploying the Application**

Create the following Services

 Service Type | Service Name | Service Plan | Name 
 --- | --- | --- | --- 
 New Relic Service | newrelic | standard | new-rel-service 
 Config Server | p-config-server | standard | config-server**
 Service Registry | p-service-registry | standard | discovery-service
 Circuit Breaker Dashboard | p-circuit-breaker-dashboard | standard | circuit-breaker-dashboard
 Database | cleardb | spark | traderdb 

** configure the service with -c and the following json

```
'{\"count\":1,\"git\":{\"uri\":\"https://github.com/pivotal-bank/cf-SpringBootTrader-config.git\"}}'
```

**Deploy µServices**

checkout and build each submodule 

```
./gradlew clean assemble
```

Push each application (update host as necessary either in push command or in manifest as noted above

```
cf p -f build/manifest.yml
```
