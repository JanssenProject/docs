# Janssen builds and configuration overview

This document details current Janssen builds configured on [Jenkins](https://jenkins.jans.io/jenkins/). 
Document capture sufficient details required to build new CI-CD infrastructure using Github Actions.


### Important build jobs


|Job Name|Purpose|Frequency|Build triggers|Test cases|Deployments?|Related GH workflow|Post build action|
| --- | --- | --- | --- | --- | --- | --- | --- |
|jans-config-api|Module build|Not scheduled|On snapshot dependency update (checks every 1 minute). On SCM update (checks every 1 minute) |jenkins-dev1.jans.io|Does not deploy|triggers `docker-jans-config-api`|Generates Cucumber HTML reports|
|DeployOpenBankingServer|5 mo
|DeployServer|14
|DeployServerIntoHost|
|DeployWar|
|FullRebuild|
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
