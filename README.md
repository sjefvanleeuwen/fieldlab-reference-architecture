# Innovative Fieldlab
**under construction**

Reference Architecture for the Fieldlab. This system is composed of docker images from projects incubated in the lab. Some of the images support generic design patterns, while others are exact implementations of standards. Together they form a working system.

Our main docker hub can be found on: https://hub.docker.com/

## Mission/Vision

The primary goal of the lab is to provide a *low code* platform that can tackle real world problems from a user centric design perspective, quickly and effortlessly during Innovative Hackathons/FieldLabs and Design Sprints with multiple stakeholders.

## Open(source) standards

### .NET Core / Standard

A free, cross platform open source implementation of the managed .NET framework. The lab develops in .NET standard whenever possible as this version is a set of APIs that all .NET platforms have to implement. This unifies the .NET platforms and prevents future fragmentation.

### Object Management Group (OMG) 

The Object Management Group (OMG) is an international, open membership, not-for-profit technology standards consortium. OMG Task Forces develop enterprise integration standards for a wide range of technologies and industries.

The lab implements the *“triple crown”* of process improvement standards. BPMN, DMN and CMMN modelling languages.

#### BPMN

To achieve process flexibility we design / model business processes in Business Process Model Notation and evaluate open source BPMN engines.  
https://www.omg.org/bpmn/


#### DMN

Decision Model and Notation (DMN) is a standard approach for describing and modeling repeatable decisions within organizations to ensure that decision models are interchangeable across organizations.  
https://www.omg.org/dmn/

#### CMMN

https://www.omg.org/cmmn/

## OpenXML

*TBA*

## OpenAPM

For application performance management (APM). We evaluate opensource components that provide means for:

* Data Collection
* Instrumentation
* Transformation and Storage
* Usage and Value Creation

## OpenID Connect and OAuth 2.0

Identity Server 4 is used for OpenID Connect. It uses the in memory-store option. The server should function for authentication/authorisation, Claims for user's and API's.

## Other API's used:

### IRMA (I Reveal My Attributes)

IRMA offers a solution to log in in a privacy-friendly way. When logging in, you reveal as a user some relevant properties (attributes) of yourself, via an IRMA app on your own mobile phone.

It does not need a public ledger, such as blockchain or a tangle to operate.


## Technical Overview
Technical layers/overview of the docker images (in Dutch/Work in Progress):

![5Layer](./doc/5layer.png)

## project references

[OpenID Connect IdentityServer4](https://github.com/sjefvanleeuwen/identity-server)  
This project dockerizes identityserver4.

[Camunda Process Template](https://github.com/sjefvanleeuwen/camunda-process-template) 
[![Build Status](https://travis-ci.org/sjefvanleeuwen/camunda-process-template.svg?branch=master)](https://travis-ci.org/sjefvanleeuwen/camunda-process-template)  
.NET Core console template that provides a harnas for deploying your BPMN flows as resources to Camunda and build external task workers.

[Camunda Business Process Engine](http://camunda.org)  [![Build Status](https://travis-ci.org/camunda/camunda-bpm-platform.svg?branch=master)](https://travis-ci.org/camunda/camunda-bpm-platform)  
Workflow and Decision Automation Platform  
Open source platform for workflow and decision automation that brings business users and software developers together.

[GEMMA Zaken](https://github.com/sjefvanleeuwen/gemma-zaken) [![Build Status](https://travis-ci.org/sjefvanleeuwen/gemma-zaken.svg?branch=master)](https://travis-ci.org/sjefvanleeuwen/gemma-zaken)  
OpenAPI .NET Core test implementations for ZDS / Zaakgericht werken

[SIGNALR Event Hub](https://github.com/sjefvanleeuwen/signalr-event-hub) 
[![Build Status](https://travis-ci.org/sjefvanleeuwen/signalr-event-hub.svg?branch=master)](https://travis-ci.org/sjefvanleeuwen/signalr-event-hub)  
Generic event hub mainly for front end signalling if an asycnhronous task has completed in the backend 

[BRP Basis Registratie Personen](https://github.com/sjefvanleeuwen/openapi-brp-fake)[![Build Status](https://travis-ci.org/sjefvanleeuwen/openapi-brp-fake.svg?branch=master)](https://travis-ci.org/sjefvanleeuwen/openapi-brp-fake)  
Provides an OpenAPI service that serves a fake BRP (Basisregistratie Personen) implementation. The implementation will generate fake addresses, names based on random seed derived from a BSN (Burger Service Nummer / Social Security Number).

[OpenXML Document Generator](https://github.com/sjefvanleeuwen/openxml-document-generator)  ![travis build](https://travis-ci.com/sjefvanleeuwen/openxml-document-generator.svg?branch=master)  
Generates documents from html into openxml standard.

[IRMA (I Reveal My Attributes) API Server](https://travis-ci.org/privacybydesign/irma_api_server)  [![Build Status](https://travis-ci.org/privacybydesign/irma_api_server.svg?branch=master)](https://travis-ci.org/privacybydesign/irma_api_server)  
This is a server that sits between IRMA tokens such as the IRMA app on the one hand, and authorized ser
vice or identity providers on the other hand. It handles all IRMA-specific cryptographic details of issuing credentials and verifying disclosure proofs on behalf of the service or identity provider. It exposes a RESTful JSON API driven by JWTs for authentication.

[MINIO Object Storage Server](https://github.com/minio/minio)  
Minio is an object storage server released under Apache License v2.0. It is compatible with Amazon S3 cloud storage service. It is best suited for storing unstructured data such as photos, videos, log files, backups and container / VM images. Size of an object can range from a few KBs to a maximum of 5TB.

## 5Layer Composition

Here's the YAML file which composes the complete references architecture for deployment on your (development) system. Please note that the IRMA server contains a private key in the config for your convenience and should therefore **not be used in production environments**.

For Azure container instances, please visit the following page:
[Container Group Deployment to Azure](./compositions/5layer/readme.md)

```yaml
version: '3.4'

services:
  # GEMMA ZDS Document Registratie OpenAPI
  drc:
    image: wigo4it/openapi-drc:alpha
    hostname: drc
    ports:
      - "5094:5094"
  # GEMMA BRP Basis Registratie Personen OpenAPI (fake generator)
  brp:
    image: wigo4it/openapi-brp-fake
    hostname: brp
    ports:
      - "5080:5080"
  # Business Process Engine, Rule Engine and Case Management  
  camunda:
    image: camunda/camunda-bpm-platform:latest
    hostname: camunda
    ports:
      - "8080:8080"
  # Intelligent Text Processing, document generator
  itp:
    image: wigo4it/oxmldocgen-api
    hostname: itp
    ports:
      - "5091:5091"
  # SignalR Event hun, websocket event streaming
  signalr-event-hub:
    image: wigo4it/signalr-event-hub
    hostname: signalr-event-hub
    ports:
      - "5051:5051"
  # OpenID Identityserver
  identity-server:
    image: wigo4it/identityserver4
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
    hostname: identity-server
    ports:
      - "5099:80"
  # Redis In Memory Key/Value Store
  redis:
    image: redis
    hostname: redis
    ports: 
      - "6379:6379"
    # MINIO S3 Bucket Storage
  minio:
    image: minio/minio
    hostname: minio
    command: server /data
    environment:
      - MINIO_ACCESS_KEY=AKIAIOSFODNN7EXAMPLE
      - MINIO_SECRET_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
    ports:
      - "9050:9000"
  # IRMA API Server
  irma-api-server:
    image: privacybydesign/irma_api_server
    hostname: irma-api-server
    ports:
      - "8088:8080"
    environment:
      - IRMA_API_CONF_BASE64_JWT_PUBLICKEY=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAxScmLzY25uKDaTldNn1cCKYOtwH5dxQtBo764zN0NZ4uwpTsq8Vyuc24LUBZMlYZfwjIDV41y9Nd2OUiDxgbEOaxVUIwJ8GQ4YEg+UdXmOeULxN0Ixdl7rM0HnRslGhu3UUbv9NBhWCBBewnA3Tr3oogzrznjDbW+JM7ahju169qAUDRM1iyhDwau87nK4/Zyjipdf0ZTWvnojlfvXpWsrSCiXYa/JSgo8wDz3kHyWO3sm1MHKFs5WZfG9J1On7ySqAzUzJOMhCt0m3hb8TimDho9nuhRkyjIl5IX7xAwJCSycCpHVVkhUY4G/+zwNb9ufSpld4JN09a0OuvtvTq0QIDAQAB
      - IRMA_API_CONF_BASE64_JWT_PRIVATEKEY=MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQDFJyYvNjbm4oNpOV02fVwIpg63Afl3FC0GjvrjM3Q1ni7ClOyrxXK5zbgtQFkyVhl/CMgNXjXL013Y5SIPGBsQ5rFVQjAnwZDhgSD5R1eY55QvE3QjF2XuszQedGyUaG7dRRu/00GFYIEF7CcDdOveiiDOvOeMNtb4kztqGO7Xr2oBQNEzWLKEPBq7zucrj9nKOKl1/RlNa+eiOV+9elaytIKJdhr8lKCjzAPPeQfJY7eybUwcoWzlZl8b0nU6fvJKoDNTMk4yEK3SbeFvxOKYOGj2e6FGTKMiXkhfvEDAkJLJwKkdVWSFRjgb/7PA1v259KmV3gk3T1rQ66+29OrRAgMBAAECggEAe6n21Z5YCbMDYrlMsqUnWXVvvXNLm1nYdEizLlhUCF3UTtFDMuuC7vEPGbNHP7+p9nj3owr5C4TlVOtE1dr0/0D08tm1gvpzej+ZA0OwuoRn+q9lJa3Djlpx0riMcvqer8Rth4Fnk9XYmHJsdkqcuNZDheoQA29SoFEZ7478IeUXAFyDRpS0EQlmUlTn5P4fEb8z3vSc7q2aY5wFUiaUKX0ugPK+777gi38h7fIwdNPY01k6jiF/97UcP/ANwt94aR3bu37mhuFeinVUi0tfjHo4LGL6P7exggK4sOQLLd2JIq3TEgMOmcALpx2LBFdXu7QjYd9wbzZwDx+aI0fQAQKBgQDzrQS6cQEGw60la1u85yi7g1Bca0RfyR+yYl6qH4H4rZGIXF9oTgZ9P0B46ZHwbgAekV+JKWZrPt26VBF1GyEo8fF6WJGQ/F1qw2Q5flMR2ueQIPrEFWA/g1TEFFVc6Fp2TS4xBtbWJg4FEbHu4xBycZYzUElqvD+So3nob6GukQKBgQDPH8b9Qcg5AYO6VD2xxAdwSBBlXZG3YQJknuVXGFQlbJRXuYxfnc3uUdb2DduK/sv18uH7mXRccJ8v/ucaKP+0t6AR+VdRFtzAzjKjoTwkPR4zcLkLaELk1/rgtokEKnWwE7UiBl1Fx3ntmK6OU5wr9UUiXMdyLJopV3g/RuoYQQKBgFRafbuI6QENdf/xJUXEg849y/DiVT4PYsCe2wRredO7Shj5WTHDaO2smsYAnTus6K+sRXU29rSDg8A/3/c5GAaTkrN2u5WEN1aBI03f1CPnMqgrMoP0nmf+L7bdDxvld4Nifm4MXwytCcdpc74troDfn05OKcwgNKWvn8D9++txAoGBAMXHXkgvLHXi0Fp4XoEE4uWAqsdgVeh5pcNXRz+nZ5Jk4DH0Z+pV0XKki1NhYCaVr0UnrEqH+ejbUeaOzTbZt3JldWA0bABuiFVDkG9XYwpnohMUrF4MLPRAmLtDEgr8UGzWJLxcv2wGUpNinCwkApinGGD7nyeTF5IqiBRELv0BAoGBAKUAFQLup+WiegQZBgplLje5wtu0fZs1rbDBG3dXEbwI1RHyMTx/Egt271+WwjcBVHKcYmHTOvdUmSzoRZJDrQpEB4EFEoUYYKlJ3+Udu8q5jcRzMs3KhTMfnriOKp6C3yRcfPcanhLfTpcTccHetUZRNxwgs9MK3LJh6pVScm+5
      - IRMA_API_CONF_ENABLE_VERIFICATION=true
      - IRMA_API_CONF_ENABLE_ISSUING=true
      - IRMA_API_CONF_ALLOW_UNSIGNED_ISSUE_REQUESTS=true
      - 'IRMA_API_CONF_AUTHORIZED_IDPS={"wigo4it": ["*"]}'

```

### Pull & Bring up the Images

```
$ docker-compose up -d
Creating 5layer_signalr-event-hub_1 ... done
Creating 5layer_itp_1               ... done
Creating 5layer_brp_1               ... done
Creating 5layer_camunda_1           ... done
Creating 5layer_identity-server_1   ... done
Creating 5layer_drc_1               ... done
Creating 5layer_redis_1             ... done
$ docker ps
CONTAINER ID        IMAGE                                 COMMAND                  CREATED             STATUS              PORTS                              NAMES
74cefb82c5c1        wigo4it/openapi-drc:alpha             "dotnet drc.dll"         2 minutes ago       Up 2 minutes        0.0.0.0:5094->5094/tcp             5layer_drc_1
e5d0be01a8bf        redis                                 "docker-entrypoint.s…"   2 minutes ago       Up 2 minutes        0.0.0.0:6379->6379/tcp             5layer_redis_1
4413fec4cb29        wigo4it/openapi-brp-fake              "dotnet Org.OpenAPIT…"   2 minutes ago       Up 2 minutes        0.0.0.0:5090->8080/tcp             5layer_brp_1
240e75000c35        wigo4it/oxmldocgen-api                "dotnet oxmldocgen-a…"   2 minutes ago       Up 2 minutes        0.0.0.0:5091->5091/tcp             5layer_itp_1
65342d42d155        wigo4it/identityserver4               "dotnet Host.dll"        2 minutes ago       Up 2 minutes        0.0.0.0:5099->80/tcp               5layer_identity-server_1
fbccc0280ea0        camunda/camunda-bpm-platform:latest   "/sbin/tini -- ./cam…"   2 minutes ago       Up 2 minutes        8000/tcp, 0.0.0.0:8080->8080/tcp   5layer_camunda_1
a904577e4f32        wigo4it/signalr-event-hub             "dotnet signalr-even…"   2 minutes ago       Up 2 minutes        0.0.0.0:5051->5051/tcp             5layer_signalr-event-hub_1
```

### Check if Redis is running

Attach a shell (not your docker container Id for Redis instance might vary:

```
$ docker exec -it e5d0be01a8bf39723c899de6ac6e602a7f71912b38de39929e67b33834c0582a /bin/sh
# redis-cli -h localhost -p 6379 ping
PONG
#
```

## Docker Container Managment (Portainer)

If you like a GUI for your docker containers. Portainer is easy to setup:

```
$ docker volume create portainer_data
$ docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

![portainer](./doc/portainer.png)


## Contributing

Pull requests are accepted

## Authors

* **Sjef van Leeuwen** - *Initial work* - [github](https://github.com/sjefvanleeuwen)

## License

This project is licensed under the GPL-V3 License - see the [LICENSE](LICENSE) file for details