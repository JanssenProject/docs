# FIDO 2

<!--
1. Architecture / Feature Overview: A high level overview of what the component is supposed to do and how it works.
1. API Reference: An [OpenAPI](https://swagger.io/specification/) document which can be viewed with [SwaggerUI](https://swagger.io/tools/swagger-ui/)
1. Code Reference: These are auto-generated docs that are extracted from the code, for example, Javadocs.
1. Deployment Reference: Instructions on how to properly deploy this component. Included are what persistence, caching, file system, network (e.g. port), compute or other system requirements are needed to make it run.
1. Data Reference: If the component needs a database or cache, an overview of the required schema or information tree.
1. Developer Reference: Want to help develop this component? This reference will tell you how to build, setup your IDE, align with best practices, and other things you'll need to know to join the team.
1. Test Reference: How to run unit tests, integration tests, performance tests, or any other kind of tests to make sure this component is running properly.
1. User guide: How to use the software? This can be administration tasks or end user functionality.
1. Security Considerations: What you need to know to operate the component securely, including best practices.
-->

## Overview

[FIDO 2.0 (FIDO2)](https://fidoalliance.org/fido2/) is an open authentication standard that enables people to leverage common devices to authenticate to online services in both mobile and desktop environments.

FIDO2 is comprised of the [W3C’s Web Authentication specification (WebAuthn)](https://www.w3.org/TR/webauthn/) and FIDO’s corresponding [Client-to-Authenticator Protocol (CTAP)](https://fidoalliance.org/specs/fido-v2.0-ps-20170927/fido-client-to-authenticator-protocol-v2.0-ps-20170927.html). WebAuthn defines a standard web API that can be built into browsers and related web platform infrastructure to enable online services to use FIDO Authentication. CTAP enables external devices such as mobile handsets or FIDO Security Keys to work with WebAuthn and serve as authenticators to desktop applications and web services.

Janssen includes a FIDO2 component to implement a two-step, two-factor authentication (2FA) with username / password as the first step, and any FIDO2 device as the second step. 

## API Reference

The API reference for the Janssen FIDO2 component is provided here <link>. 

## Code Reference

A discovery document for FIDO2 is published by the Gluu Server at: `https://<hostname>/.well-known/fido2-configuration` This document specifies the URL of the registration and authentication endpoints.

## Deployment

During Janssen installation, the administrator will have the option to also install the FIDO2 component. 

## Data

FIDO2 stores device credentials in the same persistence mechanism used by the Janssen deployment.

## Testing

## User Guide

### Credential enrollment
FIDO2 device enrollment happens automatically during the first authentication attempt. 

### Subsequent authentications
All subsequent FIDO2 authentications for that user account will require the enrolled FIDO2 key. 

### FIDO2 credential management
A user's FIDO2 devices can be removed by a Gluu administrator in LDAP under the user entry as shown in the below screenshot. <image>

<!---
![fidoldap](../img/fido2/fido2-ldap-entry.png)
--->

## Security Considerations
