# Setup Developer Workspace for Janssen

- [Prerequisites](#prerequisites)
- [Get Code and Build](#get-code-and-build)
- [Configure Jetty](#configure-jetty)
- [Setup JSON Web Keys](#setup-JSON-web-keys)
- [Setup data store](#setup-data-store)
- [Configure workspace](#configure-workspace)
- [Start Janssen Auth Server](#start-janssen-auth-server)

## Prerequisites

#### IDE

This guide uses IntellijIdea Java IDE to setup workspace. Any other IDE can be used to perform steps mentioned in this guide. IDE should support JDK version 11 or later in order to support Janssen development. Refer to Janssen documentation to find out current JDK version required by Janssen.

#### MySQL

Janssen needs persistance storage to store configuration and transactional data.
Janssen supports variety of persistance technologies including LDAP, RDBMS and cloud storage.
For this guide, we are going to use MySQL relational database as our persistance store. On a Ubuntu system, you can install MySql using command below:

```
sudo apt-get install mysql-server
```

## Get Code and Build

- open intellij and create a `new project from version control` using code URL of Github repo
```
https://github.com/JanssenProject/jans-auth-server
```
- Now we can build the project using Idea's `run/debug configurations`. Create a new `maven` configuration named `jans-auth-server-parent` and give command line as `clean install` and under `Java options` make sure that JDK-11 (any distribution) is selected. If not the you may have to install and configure JDK-11 here. Jans prefers Amazon Corretto distribution. Also, add `skip tests` option by clicking `modify` on `java options`.

- Set host name: For Janssen modules to work correctly, you need to assign a host name to your local machine's IP. localhost is not supported. To do this, we need to make changes to hosts file. On Ubuntu and other Linux destributions, this file is /etc/hosts, while for Windows same file can be found at C:\Windows\System32\drivers\etc\hosts. Make an entry similar to below in hosts file:
```
127.0.0.1       test.local.jans.io
```
Here, `test.local.jans.io` can be any name of your choice. We will refer to `test.local.jans.io` as our host name for rest of this guide.

## Configure Jetty:

Janssen uses Jetty to run application service. For the purpose of development Janssen uses Jetty Maven plug-in for quick deployments and testing. This plug-in is already a part of project dependencies, so developers do not need to add separately. Following steps will configure Jetty Maven plug-in to deploy Janssen.

### Download Jetty configuration xml files 

Download files listed below. Files will be required by Jetty to enable ssl and other configuration:
- jetty.xml: https://github.com/eclipse/jetty.project/blob/jetty-9.4.x/jetty-server/src/main/config/etc/jetty.xml
- jetty-http.xml: https://github.com/eclipse/jetty.project/blob/jetty-9.4.x/jetty-server/src/main/config/etc/jetty-http.xml
- jetty-ssl.xml: https://github.com/eclipse/jetty.project/blob/jetty-9.4.x/jetty-server/src/main/config/etc/jetty-ssl.xml 
- jetty-ssl-context.xml: https://github.com/eclipse/jetty.project/blob/jetty-9.4.x/jetty-server/src/main/config/etc/jetty-ssl-context.xml
- jetty-https.xml: https://github.com/eclipse/jetty.project/blob/jetty-9.4.x/jetty-server/src/main/config/etc/jetty-https.xml

(or you can get same files from a downloaded Jetty distribution from `<jetty-home>/etc`)

and put these files in
```
jans-auth-server/server/src/main/webapp-jetty/WEB-INF
```

### Configure Jetty for HTTPS

To configure Jetty to work with HTTPS, we have to setup certificate. We will use Java keytool to generate key pair as given below.
    
```
keytool -genkeypair -alias jetty -keyalg EC -groupname secp256r1 -keypass secret -validity 3700 -storetype JKS -keystore keystore.test.local.jans.io.jks -storepass <password-of-choice>
```

Above command will create a `.jks` file in the same directory from where you have executed the command. Copy this keystore file to  
```
jans-auth-server/server/src/main/webapp-jetty/WEB-INF
```

- update `/home/dhaval/IdeaProjects/others/jans-auth-server/server/pom.xml` with `<jettyXml>` and `contextPath` elements under `jetty-mave-plugin` as section below:

  ```
  			<plugin>
				<groupId>org.eclipse.jetty</groupId>
				<artifactId>jetty-maven-plugin</artifactId>
				<version>9.4.31.v20200723</version>
				<configuration>
					<jettyXml>src/main/webapp-jetty/WEB-INF/jetty.xml,src/main/webapp-jetty/WEB-INF/jetty-http.xml,src/main/webapp-jetty/WEB-INF/jetty-ssl.xml,src/main/webapp-jetty/WEB-INF/jetty-ssl-context.xml,src/main/webapp-jetty/WEB-INF/jetty-https.xml</jettyXml>
					<webAppConfig>
						<descriptor>${project.build.directory}/${project.build.finalName}/WEB-INF/web.xml</descriptor>
						<contextPath>/jans-auth</contextPath>
					</webAppConfig>
					<webAppSourceDirectory>${project.build.directory}/${project.build.finalName}</webAppSourceDirectory>
					<scanIntervalSeconds>3</scanIntervalSeconds>
				</configuration>
			</plugin>
  ```

- update `src/main/webapp-jetty/WEB-INF/jetty-ssl-context.xml`
  update following properties as mentioned below:
  ```
  <Set name="KeyStorePath">src/main/webapp-jetty/WEB-INF/keystore.test.local.jans.io.jks</Set>
  <Set name="KeyStorePassword">{replace with your keystore password}</Set>
  <Set name="KeyManagerPassword">{replace with your keystore password}</Set>
  <Set name="TrustStorePath">src/main/webapp-jetty/WEB-INF/keystore.test.local.jans.io.jks</Set>
  <Set name="TrustStorePassword">{replace with your keystore password}</Set>
  ```


## Setup JSON Web Keys

- Janssen source comes with keys that are required for running tests. Add these keys to keystore.

   ```
   keytool -importkeystore -srckeystore <auth-server-code-dir>/server/profiles/default/client_keystore.jks -destkeystore keystore.test.local.jans.io.jks
   ```

- Generate JWT

   - download `jans-auth-client-1.0.0-SNAPSHOT-jar-with-dependencies.jar` from `https://maven.jans.io/maven/io/jans/jans-auth-client/1.0.0-SNAPSHOT/` to `/tmp/`
   - now run 
   
   ```
   java -Dlog4j.defaultInitOverride=true -cp /tmp/jans-auth-client-1.0.0-SNAPSHOT-jar-with-dependencies.jar io.jans.as.client.util.KeyGenerator -keystore './keystore.test.local.jans.io.jks' -keypasswd secret -sig_keys RS256 RS384 RS512 ES256 ES384 ES512 -enc_keys RS256 RS384 RS512 ES256 ES384 ES512 -dnname 'CN=Jans Auth CA Certificates' -expiration 365 > /tmp/keys/keys_client_keystore.json
   ```
   
   This command adds additional keys in `keystore.test.local.jans.io.jks` and creates a JSON file with web keys. We will use web keys files later to update in our persistent store.

- At a later stage, we will point Janssen to look for certificates under `/etc/certs`. So move `keystore.test.local.jans.io.jks` file to `/etc/certs` and rename it to `jans-auth-keys.jks`.

## Setup Persistance Store

Janssen uses persistance storage to hold configuration and transactional data. 
Janssen supports variety of persistance mechanisms including LDAP, RDBMS and cloud storage.
For this guide, we are going to use MySQL relational database as a persistance store. 

As a first step, let's create a schema and a user.

- Log into MySQL

  ```
  sudo mysql
  ```
  
- Create new database(schema) for Janssen

  ```
  mysql> CREATE DATABASE jansdb;
  ```
  
- Create new db user 
  ```
  CREATE USER 'jans'@'localhost' IDENTIFIED BY 'PassOfYourChoice';
  ```
- Grant privileges to new user on `jansdb` schema 
  ```
  GRANT ALL PRIVILEGES ON jansdb.* TO 'jans'@'localhost';
  ```
- Exit MySQL login 

### Load Initial Data Set

Next, we will load basic configuration data into MySQL. This data is required
by Janssen modules at the time of start up. This data set also contains test data set to help us run integration tests. We will use a helper script that will create required tables and also insert basic configuration and test data.

- Get MySQL database data import script file from [here](TODO add link here). This 
script is a data dump which can be directly loaded into local MySQL database. This script is a generic script and we have to edit certain values as per our local setup as described in steps below.

#### Update hostname
we need to replace generic host name in the script with the one that we have set for our local environment, which is `test.local.jans.io`. To do that, open script in a text editor and
  
      ```
      TODO replace `testmysql.dd.jans.io` with actual host name from final script in steps below
      ```
  -   replace string `https://testmysql.dd.jans.io` with `https://test.local.jans.io:8443` 

  -   replace string `testmysql.dd.jans.io` with `test.local.jans.io`


#### Update keystore secret in database config

- Search for string `keyStoreSecret` in script and replace corresponding value. Here the `<key-store-secret>` is the secret you used while creating keystore in [Setup SSL](#setup-ssl) step.

- Script is now ready to be executed. Import data load script into your local MySQL

  ```
  sudo mysql -u root -p jansdb < jansdb_dump.sql;
  ```

#### Update JSON Web keys in database config

Now we need to update JSON web keys in DB with what we have generated.

- open `/tmp/keys/keys_client_keystore.json` and copy the content
- login to mysql with user `jans`
  ```
  sudo mysql -u jans -p jansdb
  ```
- Run the update query as below:
  ```
  UPDATE jansdb.jansAppConf SET jansConfWebKeys = '<multiline content from keystore json>' where doc_id = "jans-auth";
  ```

At this point, database is ready to support Janssen server.

## Configure Properties

Now that we have configured Jetty plug-in and persistent store, we need to update properties files in our code base accordingly.

In our code base, under `server/conf` directory, we have two property files, `jans.properties` and `jans-sql.properties`.
  - `jans.properties`: Holds details like type of persistance to use, localtion of certificates etc.
  - `jans-sql.properties`: Since we are using MySQL RDBMS persistence store, 
    details in this file will be used to connect MySQL.
    
- Update `jans.properties`
  -   edit `persistence.type` to `persistence.type= sql` since we are using MySQL as our backend
  -   edit `certsDir` to `certsDir=/etc/certs`
  -   edit value of `jansAuth_ConfigurationEntryDN` to `jansAuth_ConfigurationEntryDN=ou=jans-auth,ou=configuration,o=jans` 

- Update `jans-sql.properties`
  - Set `db.schema.name` to name of schema that will be used to persist Janssen data. We will name the schema as `jansdb` for this guide. 
  - Set `connection.uri` to `jdbc:mysql://localhost:3306/jansdb`
  - Set `connection.driver-property.serverTimezone` to `UTC`
  - Set `auth.userName` to the user that Janssen server will use to access database. For this guide, will name it as `jans`
  - Set `auth.userPassword` to passwod that you would want for `jans` user
  - Set `password.encryption.method` to method you have selected to encrypt the password for `userPassword` property. If you are using plain text password for your local setup, comment out this property.
    
 Properties of `jans-sql.properies` listed above are most likely to be customised as per your local setup. 
 Other properties from this file can be set to standard values as given below.

  ```
  # Connection pool size
  connection.pool.max-total=40
  connection.pool.max-idle=15
  connection.pool.min-idle=5

  # Max time needed to create connection pool in milliseconds
  connection.pool.create-max-wait-time-millis=20000

  # Max wait 20 seconds
  connection.pool.max-wait-time-millis=20000

  # Allow to evict connection in pool after 30 minutes
  connection.pool.min-evictable-idle-time-millis=180000
  ```


## Start Janssen Auth Server

Now we are ready to run the app.

### Using Maven
```
dhaval@thinkpad:~/IdeaProjects/others/jans-auth-server/server$ mvn -DskipTests -Djans.base=./target -Dlog.base=/home/dhaval/temp/logs/jans-logs jetty:run-war
```

### Using Idea run configuration

- Open Idea's `run/debug configurations` and create a copy of previously created run configuration `jans-auth-server-parent`, and 
  - change working directory to `jans-auth-server` by selecting `jans-auth-server/server`
  - change the `command line` arguments to `jetty:run-war`
  - Add following `VM arguments` to `java options`
   ```
   -Djans.base=./target -Dlog.base=<dir-for-logs>
   ```

- access `http://test.local.jans.io:8080/jans-auth/.well-known/openid-configuration`
- for https: `https://test.local.jans.io:8443/jans-auth/.well-known/openid-configuration`
- access `http://test.local.jans.io:8080/jans-auth/restv1/userinfo` (expect to get error JSON due to missing params)
- access `http://test.local.jans.io:8080/jans-auth/restv1/clientinfo` (expect to get error JSON due to missing params)

## Steps:
  - [Setup workspace](https://gist.github.com/ossdhaval/c0c82e437dcb5d5403f241e81908ec4c)	
  - [Run unit and integration tests](https://gist.github.com/ossdhaval/f2ca2590cdbe0c11db5d58f87e13479f)
  - [Setup Debugging](https://gist.github.com/ossdhaval/11df8be8ebf9063b2ba18097efb040f9)
  - [Setup IntellijIdea](https://gist.github.com/ossdhaval/36e219c350e1120b31f803695a22e30d)
