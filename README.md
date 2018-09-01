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

## Technical Overview
Technical layers/overview of the docker images (in Dutch/Work in Progress):

![5Layer](./doc/5layer.png)

## project references

[OpenID Connect IdentityServer4](https://github.com/sjefvanleeuwen/identity-server)  
This project dockerizes identityserver4.

[Camunda Process Template](https://github.com/sjefvanleeuwen/camunda-process-template) 
[![Build Status](https://travis-ci.org/sjefvanleeuwen/camunda-process-template.svg?branch=master)](https://travis-ci.org/sjefvanleeuwen/camunda-process-template)  
.NET Core console template that provides a harnas for deploying your BPMN flows as resources to Camunda and build external task workers.

[Camunda Business Process Engine](http://camunda.org)  
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

## 5Layer Composition

```yaml
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
      - "5090:8080"
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
    hostname: identity-server
    ports:
      - "5099:80"
```

### Pull & Bring up the Images

```powershell
PS C:\Users\********\fieldlab-reference-architecture\compositions\5Layer> docker-compose pull
Pulling drc               ... done
Pulling brp               ... done
Pulling camunda           ... done
Pulling itp               ... done
Pulling signalr-event-hub ... done
PS C:\Users\********\fieldlab-reference-architecture\compositions\5Layer> docker-compose up -d
Creating 5layer_camunda_1           ... done
Creating 5layer_itp_1               ... done
Creating 5layer_signalr-event-hub_1 ... done
Creating 5layer_brp_1               ... done
Creating 5layer_drc_1               ... done
PS C:\Users\********\fieldlab-reference-architecture\compositions\5Layer> docker ps
CONTAINER ID        IMAGE                                 COMMAND                  CREATED             STATUS              PORTS                              NAMES
4c8fb39a9e86        wigo4it/openapi-drc:alpha             "dotnet drc.dll"         23 minutes ago      Up 23 minutes       0.0.0.0:5094->5094/tcp             5layer_drc_1
d6b0e05d1cf6        wigo4it/signalr-event-hub             "dotnet signalr-even…"   23 minutes ago      Up 23 minutes       0.0.0.0:5051->5051/tcp             5layer_signalr-event-hub_1
e207a43a1266        wigo4it/openapi-brp-fake              "dotnet Org.OpenAPIT…"   23 minutes ago      Up 23 minutes       0.0.0.0:5090->8080/tcp             5layer_brp_1
0c2010d23fcc        camunda/camunda-bpm-platform:latest   "/sbin/tini -- ./cam…"   23 minutes ago      Up 23 minutes       8000/tcp, 0.0.0.0:8080->8080/tcp   5layer_camunda_1
8af00b741dd8        wigo4it/oxmldocgen-api                "dotnet oxmldocgen-a…"   23 minutes ago      Up 23 minutes       0.0.0.0:5091->5091/tcp             5layer_itp_1
```

## Contributing

Pull requests are accepted

## Authors

* **Sjef van Leeuwen** - *Initial work* - [github](https://github.com/sjefvanleeuwen)

## License

This project is licensed under the GPL-V3 License - see the [LICENSE](LICENSE) file for details