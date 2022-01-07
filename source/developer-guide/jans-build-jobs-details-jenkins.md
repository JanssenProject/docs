# Janssen builds and configuration overview

This document details current Janssen builds configured on [Jenkins](https://jenkins.jans.io/jenkins/). 
Document capture sufficient details required to build new CI-CD infrastructure using Github Actions.


### Important build jobs

#### FullRebuild
- [Link](https://jenkins.jans.io/jenkins/job/FullRebuild/)
- Purpose
  - This pipeline builds, deploys and tests all module of Jans server. First this pipeline builds modules(without tests) using respective jobs in this order: 'jans-bom','jans-core','jans-orm','jans-notify','jans-eleven','jans-fido2','jans-auth-server','jans-scim','jans-client-api,'jans-config-api'.|
- Input Params
  - VERSION_NAME:master 
  - MAVEN_SKIP_TESTS:false
  - PROFILE_NAME:jenkins-build.jans.io
  - CONTAINER_NAME_LDAP:ubuntu20-ldap
  - CONTAINER_NAME_CB:ubuntu20-couchbase
  - CONTAINER_NAME_SPANNER:ubuntu20-spanner
  - CONTAINER_NAME_MYSQL:ubuntu20-mysql
- Artifacts
  - None
- Frequency
  - Scheduled twice a day (around 2am and 12pm)
- Test cases
  - Runs tests for different persistence backend setups. Each for LDAP, CB, spanner, MySQL. To run tests, a new server is setup on LxC using `install.py` and then tests for `jans-auth-server`, `scim` and `jans-config-api` are executed against new server. For each of this task it triggers a downstream job. For example, runs `deployserver` job for deploying server for various backends, `jans-auth-server-cb` for running tests against CB backend
- Deployments
  - Deploys server on `jenkins-dev1.jans.io`
- Related GH workflow
  - None
- Post build action
  - RocketChat notifications for success, unstable and failure

#### DeployServer
- [Link](https://jenkins.jans.io/jenkins/job/DeployServer/)
- Purpose
  - This job is used to install Janssen server on an LxC container hosted on a remote server. Installed Janssen server is then used to run integration tests.
- Input Params
  - SERVER_NAME: jenkins-dev1.jans.io
  - CONTAINER_NAME: ubuntu20-ldap
  - PERSISTENCE_DB: ldap
  - VERSION_NAME: master
  - DIST_SERVER_BASE: https://maven.jans.io/maven
- Artifacts
  - None
- Frequency
  - Not scheduled but invoked by other jobs like fullrebuild
- Test cases
  - None
- Deployments
  - Deploys Janssen server on $SERVER_NAME
- Related GH workflow
  - None
- Post build action
  - None

#### jans-auth-server
- [Link](https://jenkins.jans.io/jenkins/job/jans-auth-server/)
- Purpose
  - Builds, test and deploy `jans-auth-server` module
- Input Params
  - VERSION_NAME: master
  - PROFILE_NAME: jenkins-dev1.jans.io
  - MAVEN_SKIP_TESTS: false
  - DEVELOPMENT_BUILD: false
  - DEPLOY_BUILD: false
  - CVSS_SCORE: 9
  - SKIP_FINDBUGS: true
  - DEPENDENCY_CHECK: false
  - TEST_CONF_IN_HOST: false
  - CONTAINER_NAME: ubuntu20-ldap
- Artifacts
  - jans-auth-model-1.0.0-SNAPSHOT.jar, jans-auth-model-1.0.0-SNAPSHOT.pom
  - jans-auth-persistence-model-1.0.0-SNAPSHOT.jar, jans-auth-persistence-model-1.0.0-SNAPSHOT.pom
  - jans-auth-client-1.0.0-SNAPSHOT.jar, jans-auth-client-1.0.0-SNAPSHOT.pom
  - jans-auth-client-1.0.0-SNAPSHOT-jar-with-dependencies.jar, 
  - jans-auth-static-1.0.0-SNAPSHOT.jar, jans-auth-static-1.0.0-SNAPSHOT.pom
  - jans-auth-common-1.0.0-SNAPSHOT.jar,jans-auth-common-1.0.0-SNAPSHOT.pom
  - jans-auth-server-1.0.0-SNAPSHOT.war, jans-auth-server-1.0.0-SNAPSHOT.pom
  - jans-auth-server-1.0.0-SNAPSHOT.jar
- Frequency
  - Not scheduled but invoked by other jobs like fullrebuild
- Test cases
  - runs tests against existing server: jenkins-dev1.jans.io
- Deployments
  - Deploys Janssen server on $SERVER_NAME
- Related GH workflow
  - None
- Post build action
  - publish javadocs
  - publish OWASP report
  - publish testNG xml report







|Job Name|Purpose|Input params|Artifacts|Frequency|Test cases|Deployments?|Related GH workflow|Post build action|
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
|jans-config-api|Module build|Not scheduled|On snapshot dependency update (checks every 1 minute). On SCM update (checks every 1 minute) |jenkins-dev1.jans.io|Does not deploy|triggers `docker-jans-config-api`|Generates Cucumber HTML reports|
|DeployOpenBankingServer|5 mo||||||
|DeployServer|14||||||
|DeployServerIntoHost|||||||
|DeployWar|||||||

|jans-auth-server|
|jans-auth-server-cb|Wrapper around `jans-auth-server` to pass relevant container name||||||
|jans-auth-server-extending_crypto_support|
|jans-auth-server-issue_142|
|jans-auth-server-ldap|Wrapper around `jans-auth-server` to pass relevant container name||||||
|jans-auth-server-mysql|Wrapper around `jans-auth-server` to pass relevant container name||||||
|jans-auth-server-spanner|Wrapper around `jans-auth-server` to pass relevant container name||||||
|jans-bom|
|jans-client-api|
|jans-config-api-bkp|
|jans-config-api-cb|Wrapper around `jans-config-api` to pass relevant container name||||||
|jans-config-api-ldap|Wrapper around `jans-config-api` to pass relevant container name||||||
|jans-config-api-mysql|Wrapper around `jans-config-api` to pass relevant container name||||||
|jans-config-api-spanner|Wrapper around `jans-config-api` to pass relevant container name||||||
|jans-config-api_bkp|
|jans-core|
|jans-eleven|
|jans-fido2|
|jans-notify|
|jans-orm|
|jans-scim|
|jans-scim-cb|Wrapper around `jans-scim` to pass relevant container name||||||
|jans-scim-ldap|Wrapper around `jans-scim` to pass relevant container name||||||
|jans-scim-mysql|Wrapper around `jans-scim` to pass relevant container name||||||
|jans-scim-spanner|Wrapper around `jans-scim` to pass relevant container name||||||
|StartServerContainer|
