# Janssen builds and configuration overview

This document details current Janssen builds configured on [Jenkins](https://jenkins.jans.io/jenkins/). 
Document capture sufficient details required to build new CI-CD infrastructure using Github Actions.


### Important build jobs

#### FullRebuild

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




- Purpose
- Input Params
- Artifacts
- Frequency
- Test cases
- Deployments
- Related GH workflow
- Post build action


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
