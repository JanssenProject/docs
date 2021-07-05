# SCIM

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

SCIM is a specification designed to reduce the complexity of user management operations by providing a common user schema and the patterns for exchanging such schema using HTTP in a platform-neutral fashion. The aim of SCIM is achieving interoperability, security, and scalability in the context of identity management.

Developers can think of **SCIM** merely as a **REST API** with endpoints exposing **CRUD** functionality (create, read, update and delete).

For your reference, the current version of the standard is governed by the following documents: [RFC 7642](https://tools.ietf.org/html/rfc7642), [RFC 7643](https://tools.ietf.org/html/rfc7643), and [RFC 7644](https://tools.ietf.org/html/rfc7644).

## API Reference

<!-- API documentation needs to be generated and linked here -->

## Code Reference

<!-- Double check that SCIM Javadocs aren't needed-->

## Deployment

During Janssen installation, select the option to also install the SCIM component.

## Data

## Testing

## User Guide

## Security Considerations

Clearly, this API must not be anonymously accessed. However, the basic SCIM standard does not define a specific mechanism to prevent unauthorized requests to endpoints. There are just a few guidelines in section 2 of [RFC 7644](https://tools.ietf.org/html/rfc7644) concerned with authentication and authorization. 

Janssen allows you to protect your endpoints with UMA (a profile of [OAuth 2.0](http://tools.ietf.org/html/rfc6749)). This is a safe and standardized approach for controlling access to web resources. For SCIM protection, we **strongly recommend** its usage. 

Alternatively, for testing purposes you can temporarily enable the test mode. In this mode, some complexity is taken out so that it serves as a quick and easy way to start interacting with your service, as well as learning about SCIM.

