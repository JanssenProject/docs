# Janssen builds and configuration overview

This document details current Janssen builds configured on [Jenkins](https://jenkins.jans.io/jenkins/). 
Document capture sufficient details required to build new CI-CD infrastructure using Github Actions.


## Important build jobs

### FullRebuild
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

### DeployServer
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

### jans-auth-server
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
  - triggers on SCM change
  - triggers on maven dependency change
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

### jans-config-api

- [Link](https://jenkins.jans.io/jenkins/job/jans-config-api/)
- Purpose
  - Builds and test `jans-config-api` module
- Input Params
  - VERSION_NAME: master
  - PROFILE_NAME: jenkins-dev1.jans.io
  - MAVEN_SKIP_TESTS: false
  - DEVELOPMENT_BUILD: false
  - TEST_CONF_IN_HOST: false
  - CONTAINER_NAME: ubuntu20-ldap
- Artifacts
  - jans-config-api-parent-1.0.0-SNAPSHOT.pom
  - jans-config-api-common-1.0.0-SNAPSHOT.jar, jans-config-api-common-1.0.0-SNAPSHOT.pom
  - jans-config-api-server-1.0.0-SNAPSHOT.war, jans-config-api-server-1.0.0-SNAPSHOT.pom
  - jans-config-api-server-1.0.0-SNAPSHOT-tests.jar
  - jans-config-api-server-1.0.0-SNAPSHOT.jar
  - plugins-1.0.0-SNAPSHOT.pom
  - admin-ui-plugin-1.0.0-SNAPSHOT.jar, admin-ui-plugin-1.0.0-SNAPSHOT.pom
  - admin-ui-plugin-1.0.0-SNAPSHOT-distribution.jar
  - jans-config-api-shared-1.0.0-SNAPSHOT.pom
  - scim-plugin-1.0.0-SNAPSHOT.jar, scim-plugin-1.0.0-SNAPSHOT.pom
  - scim-plugin-1.0.0-SNAPSHOT-distribution.jar
- Frequency
  - Not scheduled but invoked by other jobs like fullrebuild
  - triggers on SCM change
  - triggers on maven dependency change
- Test cases
  - runs tests against existing server: jenkins-dev1.jans.io
- Deployments
  - doesn't deploy
- Related GH workflow
  - https://api.github.com/repos/JanssenProject/jans-cloud-native/actions/workflows/15443035
- Post build action
  - publish javadocs
  - publish OWASP report
  - publish testNG xml report

### jans-scim

- [Link](https://jenkins.jans.io/jenkins/job/jans-scim/)
- Purpose
  - Builds and test `jans-scim` module
- Input Params
  - VERSION_NAME: master
  - PROFILE_NAME: jenkins-dev1.jans.io
  - MAVEN_SKIP_TESTS: false
  - DEVELOPMENT_BUILD: false
  - TEST_CONF_IN_HOST: false
  - CONTAINER_NAME: ubuntu20-ldap
- Artifacts
  - jans-scim-1.0.0-SNAPSHOT.pom
  - jans-scim-model-1.0.0-SNAPSHOT.jar, jans-scim-model-1.0.0-SNAPSHOT.pom
  - jans-scim-client-1.0.0-SNAPSHOT.jar, jans-scim-client-1.0.0-SNAPSHOT.pom
  - jans-scim-service-1.0.0-SNAPSHOT.jar, jans-scim-service-1.0.0-SNAPSHOT.pom
  - jans-scim-server-1.0.0-SNAPSHOT.war, jans-scim-server-1.0.0-SNAPSHOT.pom
- Frequency
  - Not scheduled but invoked by other jobs like fullrebuild
  - triggers on SCM change
  - triggers on maven dependency change
- Test cases
  - runs tests against existing server: jenkins-dev1.jans.io
- Deployments
  - doesn't deploy
- Related GH workflow
  - https://api.github.com/repos/JanssenProject/jans-cloud-native/actions/workflows/15443035
- Post build action
  - publish javadocs
  - publish findbugs report
  - publish testNG xml report


### jans-client-api

- [Link](https://jenkins.jans.io/jenkins/job/jans-client-api/)
- Purpose
  - Builds and test `jans-client-api` module
- Input Params
  - VERSION_NAME: master
  - PROFILE_NAME: default
  - MAVEN_SKIP_TESTS: false
  - CLIENT_API_CONF: /home/tomcat/.jenkins/jobs/oxD_4.2/workspace/oxd-server/src/test/resources/oxd-conf-test.json
  - SERVER_PROFILE: jenkins-dev1.jans.io
- Artifacts
  - jans-client-api-parent-1.0.0-SNAPSHOT.pom
  - uma-rs-core-1.0.0-SNAPSHOT.jar
  - uma-rs-core-1.0.0-SNAPSHOT.pom
  - uma-rs-core-1.0.0-SNAPSHOT-sources.jar
  - uma-rs-core-1.0.0-SNAPSHOT-tests.jar
  - uma-rs-resteasy-1.0.0-SNAPSHOT.jar
  - uma-rs-resteasy-1.0.0-SNAPSHOT.pom
  - uma-rs-resteasy-1.0.0-SNAPSHOT-tests.jar
  - uma-rs-resteasy-1.0.0-SNAPSHOT-sources.jar
  - jans-client-api-common-1.0.0-SNAPSHOT.jar
  - jans-client-api-common-1.0.0-SNAPSHOT.pom
  - jans-client-api-common-1.0.0-SNAPSHOT-tests.jar
  - jans-client-api-common-1.0.0-SNAPSHOT-sources.jar
  - jans-client-api-1.0.0-SNAPSHOT.jar
  - jans-client-api-1.0.0-SNAPSHOT.pom
  - jans-client-api-1.0.0-SNAPSHOT-tests.jar
  - jans-client-api-1.0.0-SNAPSHOT-sources.jar
  - jans-client-api-gen-1.0.0-SNAPSHOT.jar
  - jans-client-api-gen-1.0.0-SNAPSHOT.pom
  - jans-client-api-gen-1.0.0-SNAPSHOT-tests.jar
  - jans-client-api-gen-1.0.0-SNAPSHOT-javadoc.jar
  - jans-client-api-gen-1.0.0-SNAPSHOT-sources.jar
  - jans-client-api-server-1.0.0-SNAPSHOT.jar
  - jans-client-api-server-1.0.0-SNAPSHOT.pom
  - jans-client-api-server-1.0.0-SNAPSHOT-tests.jar
  - jans-client-api-server-1.0.0-SNAPSHOT-sources.jar
  - jans-client-api-server-1.0.0-SNAPSHOT-distribution.zip
- Frequency
  - Not scheduled but invoked by other jobs like fullrebuild
  - triggers on SCM change
  - triggers on maven dependency change
- Test cases
  - runs tests against existing server: jenkins-dev1.jans.io. But target server is not defined by server profile as in other projects but it is configured using `test/test.properties`
- Deployments
  - doesn't deploy
- Related GH workflow
  - https://api.github.com/repos/JanssenProject/jans-cloud-native/actions/workflows/15443035
- Post build action
  - publish testNG xml report


### jans-core

- [Link](https://jenkins.jans.io/jenkins/job/jans-core/)
- Purpose
  - Builds and test `jans-core` module
- Input Params
  - VERSION_NAME: master
  - MAVEN_SKIP_TESTS: false
- Artifacts
  - Produces 17 jar files as a result of maven build
- Frequency
  - Not scheduled but invoked by other jobs like fullrebuild
  - triggers on SCM change
  - triggers on maven dependency change
- Test cases
  - only few unit tests exist. Not needed to run against a server.
- Deployments
  - doesn't deploy
- Related GH workflow
  - none
- Post build action
  - publish testNG xml report


### jans-fido2

- [Link](https://jenkins.jans.io/jenkins/job/jans-fido2/)
- Purpose
  - Builds and test `jans-fido2` module
- Input Params
  - VERSION_NAME: master
  - PROFILE_NAME: default
  - MAVEN_SKIP_TESTS: false
  - DEVELOPMENT_BUILD: false
- Artifacts
  - jans-fido2-parent-1.0.0-SNAPSHOT.pom
  - jans-fido2-model-1.0.0-SNAPSHOT.jar, jans-fido2-model-1.0.0-SNAPSHOT.pom
  - jans-fido2-client-1.0.0-SNAPSHOT.jar, jans-fido2-client-1.0.0-SNAPSHOT.pom
  - jans-fido2-server-1.0.0-SNAPSHOT.war, jans-fido2-server-1.0.0-SNAPSHOT.pom
- Frequency
  - Not scheduled but invoked by other jobs like fullrebuild
  - triggers on SCM change
  - triggers on maven dependency change
- Test cases
  - No tests available.
- Deployments
  - doesn't deploy
- Related GH workflow
  - https://api.github.com/repos/JanssenProject/jans-cloud-native/actions/workflows/15443035
- Post build action
  - publish testNG xml report

### jans-notify

- [Link](https://jenkins.jans.io/jenkins/job/jans-notify/)
- Purpose
  - Builds and test `jans-notify`` module
- Input Params
  - VERSION_NAME: master
  - MAVEN_SKIP_TESTS: false
- Artifacts
  - jans-notify-parent-1.0.0-SNAPSHOT.pom
  - jans-notify-model-1.0.0-SNAPSHOT.jar, jans-notify-model-1.0.0-SNAPSHOT.pom
  - jans-notify-client-1.0.0-SNAPSHOT.jar, jans-notify-client-1.0.0-SNAPSHOT.pom
  - jans-notify-client2-1.0.0-SNAPSHOT.jar, jans-notify-client2-1.0.0-SNAPSHOT.pom
  - jans-notify-server-1.0.0-SNAPSHOT.war, jans-notify-server-1.0.0-SNAPSHOT.pom
- Frequency
  - Not scheduled but invoked by other jobs like fullrebuild
  - triggers on SCM change
  - triggers on maven dependency change
- Test cases
  - No tests available.
- Deployments
  - doesn't deploy
- Related GH workflow
  - none
- Post build action
  - publish testNG xml report
