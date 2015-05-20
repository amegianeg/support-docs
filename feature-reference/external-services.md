An **external service** is an object that represents a service which is deployed and managed outside of the scope of Tutum. External services behave as regular services when being linked via [Service links]().

Examples of third party services include:

* Third party services like Amazon's RDS
* Services that run on dedicated infrastructure for security or performance reasons

**External services** are defined by a combination of the following:

* A **name** for the service
* A **FQDN** where the service can be accessed
* One or more **ports** that the service exposes
* (Optionally) one or more **environment variables** to be propagated

Your regular services can link to an external service in the same way they link any other service. You can provide a link name, and all environment variables and hostnames will be available in your service as described in the [Service links]() article.


## Creating an external service

### Using the web UI


### Using the API


### Using the CLI


## Linking to an external service as part of a stack

