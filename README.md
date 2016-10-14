# Spring1PlatformDemo-Metrics

Demo project used to demonstrate PCF metrics and New Relic integration.  Originally a copy of code from this project https://github.com/pivotal-bank/cf-SpringBootTrader.  Some slight variation added to support desire to load app for metrics.

Each submodule projects manifest-unversioned.yml file should be updated to change the host to be the space that the demo would be run in &lt;&lt;submodule&gt;&gt;-&lt;&lt;space&gt;&gt; e.g. quotes-s1p12.  See **Deploy µServices** section below for more details

#How to Use this Demo

This demo provides a means to deploy the Spring Trader µServices Application and place load on it so that the automatically included PCF Metrics features can be show from the Apps Manager.  

Addtionally, the deployment described allows for APM integration via service binding/Java Buildpack.  (In this case the PWS New Relic service) 

Typically it is best to pre deploy the application to ensure there are no metrics streaming delays during a live demo. (Especially from APM solutions) 


#Deploying the Application

Create the following Services

 Service Type | Service Name | Service Plan | Name 
 --- | --- | --- | --- 
 New Relic Service (PWS only) | newrelic | standard | new-rel-service 
 Config Server | p-config-server | standard | config-server**
 Service Registry | p-service-registry | standard | discovery-service
 Circuit Breaker Dashboard | p-circuit-breaker-dashboard | standard | circuit-breaker-dashboard
 Database-PWS | cleardb | spark | traderdb 
 Database-PCF | p-mysql | 100mb-dev (in Mysql 1.8 the plan is now "100mb") | traderdb 

** configure the service with -c and the following json

```
'{"count":1,"git":{"uri":"https://github.com/pivotal-bank/cf-SpringBootTrader-config.git"}}'
```

##Deploy µServices

checkout and build each submodule 

```
./gradlew clean assemble
```

Push each µService (update host as necessary either in push command or in manifest as noted above) in the following order

(Note in a PCF environment you will need to execute the following prior to staging, or restaging, in order to connect to Spring Cloud Services)

```
cf set-env <<appname>> CF_TARGET https://<<apiendpoint.yourdomain.com>>
```

1. quotes-service
2. accounts-service
3. portfolio-service
4. web-ui

```
cf p -f build/manifest.yml
```

##Data Setup

The µServices requiring test data (accounts and portfolio) include scripts that initialize the database with the test data on first database connect

**Providing Application Load for Metrics**

Jmeter load scripts are included in the loadscripts directory.  These scripts can be deployed using a jmeter buildpack which will containerize and run them in headless mode (see - https://github.com/aripka-pivotal/cf-buildpack-jmeter).

The buildpack uses the environment variables TEST_URL and LOAD_SCRIPT
LOAD_SCRIPT - the jmeter script to use
TEST_URL - the µService path (e.g - webtrader-s1p12.cfapps.io)

These are defined in the manifest files that can be updated for specfic deployments

To push to cloud foundry navigate to the loadscripts directory (the artifact is all the files in the directory) 
