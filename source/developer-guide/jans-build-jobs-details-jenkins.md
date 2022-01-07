# Janssen builds and configuration overview

This document details current Janssen builds configured on [Jenkins](https://jenkins.jans.io/jenkins/). 
Document capture sufficient details required to build new CI-CD infrastructure using Github Actions.


### Important build jobs


|Job Name|Purpose|Frequency|Test cases|Deployments?|Related GH workflow|Post build action|
| --- | --- | --- | --- | --- | --- | --- |
|FullRebuild|This pipeline builds, deploys and tests all module of Jans server. First this pipeline builds modules(without tests) using respective jobs in this order: 'jans-bom','jans-core','jans-orm','jans-notify','jans-eleven','jans-fido2','jans-auth-server','jans-scim','jans-client-api,'jans-config-api'.|Scheduled twice a day (around 2am and 12pm)|Runs tests for different persistence backend setups. Each for LDAP, CB, spanner, MySQL. To run tests, a new server is setup on LxC using `install.py` and then tests for `jans-auth-server`, `scim` and `jans-config-api` are executed against new server. For each of this task it triggers a downstream job. For example, runs `deployserver` job for deploying server for various backends, `jans-auth-server-cb` for running tests against CB backend|Deploys server on `jenkins-dev1.jans.io`|||
|jans-config-api|Module build|Not scheduled|On snapshot dependency update (checks every 1 minute). On SCM update (checks every 1 minute) |jenkins-dev1.jans.io|Does not deploy|triggers `docker-jans-config-api`|Generates Cucumber HTML reports|
|DeployOpenBankingServer|5 mo
|DeployServer|14
|DeployServerIntoHost|
|DeployWar|

|jans-auth-server|
|jans-auth-server-cb|
|jans-auth-server-extending_crypto_support|
|jans-auth-server-issue_142|
|jans-auth-server-ldap|
|jans-auth-server-mysql|
|jans-auth-server-spanner|
|jans-bom|
|jans-client-api|
|jans-config-api-bkp|
|jans-config-api-cb|
|jans-config-api-ldap|
|jans-config-api-mysql|
|jans-config-api-spanner|
|jans-config-api_bkp|
|jans-core|
|jans-eleven|
|jans-fido2|
|jans-notify|
|jans-orm|
|jans-scim|
|jans-scim-cb|
|jans-scim-ldap|
|jans-scim-mysql|
|jans-scim-spanner|
|StartServerContainer|
