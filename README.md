# Keycloak
* Keycloak is an open source software product to allow single sign-on with Identity Management and Access Management aimed at modern applications and services.

* Keycloak is Open source and developed in Java and carried by Redhat.
* It offers several adapters / connectors Spring Boot & Spring Security & Tomcat, JavaScript, NodeJS, Android…
* Support of openID Connect (an extension of OAuth 2.0) and SAML 2.0.
* It offers a REST API for the administration part.
* It offers an administrative console for centralized user management.
* It allows session management / Prize in charge of CORS

## Features
Among the many features of Keycloak include :
* User Registration
* Social login
* Single Sign-On/Sign-Off across all applications belonging to the same Realm
* 2-factor authentication

## Configure keycloak with PostgreSQL database
Keycloak comes with its own embedded Java-based relational database called H2.
It is highly recommended that you replace it with a more production ready external database. The H2 database is not very viable in high concurrency situations and should not be used in a cluster either
The purpose of this guide is to show you how to connect Keycloak to postgreSQL database

### Steps
1. Package the JDBC Driver  
    Find and download the JDBC driver JAR for postgreSQL. Before you can use this driver, you must package it up into a module and install it into the server:  
    * Within the following directory \modules\system\layers\keycloak\org\ , create postgresql\main and copy your database driver JAR into this directory and create an empty module.xml file within it too.  
    ![Image](https://github.com/Wassimkal-projects/keycloak/blob/master/postgresql-driver.JPG) 

2. After you have done this, open up the module.xml file and create the following XML:
    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.3" name="org.postgresql">
    
        <resources>
            <resource-root path="postgresql-9.4.1212.jar"/>
        </resources>
    
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module> 
    ``` 
3. Declare and Load JDBC Driver
    * If you’re deploying in standard mode, edit …​/standalone/configuration/standalone.xml
    * If you’re deploying in standard clustering mode, edit …​/standalone/configuration/standalone-ha.xml
    * If you’re deploying in domain mode, edit …​/domain/configuration/domain.xml.
    
    Within the profile, search for the drivers XML block within the datasources subsystem and declare postgreSQL driver:  
    ```xml
             <datasources>
             ...
               <drivers>
                  <driver name="postgresql" module="org.postgresql">
                      <xa-datasource-class>org.postgresql.xa.PGXADataSource</xa-datasource-class>
                  </driver>
                  <driver name="h2" module="com.h2database.h2">
                      <xa-datasource-class>org.h2.jdbcx.JdbcDataSource</xa-datasource-class>
                  </driver>
               </drivers>
             </datasources>
    ```
4. Modify the Keycloak Datasource
    ```xml
     <datasources>
       ...
       <datasource jndi-name="java:jboss/datasources/KeycloakDS" pool-name="KeycloakDS" enabled="true" use-java-context="true">
           <connection-url>jdbc:postgresql://localhost/keycloak</connection-url>
           <driver>postgresql</driver>
           <pool>
               <max-pool-size>20</max-pool-size>
           </pool>
           <security>
               <user-name>wk-projects</user-name>
               <password>admin</password>
           </security>
       </datasource>
        ...
     </datasources>
    ```
5. Socket Port Bindings
Search for socket-binding-group to overridden the pre-defined ports 

6. Run the server using cmd under /bin: use an offset to override the port
```
standalone.bat -Djboss.socket.binding.port-offset=100
```
