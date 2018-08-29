# fieldlab-reference-architecture

Reference Architecture for Fieldlabs. This system is composed of docker images from other projects. Some of the images support generic design patterns, while others are exact implementations of standards. Together they form a working system.

## project references

[Camunda Business Process Engine](http://camunda.org)  
Workflow and Decision Automation Platform  
Open source platform for workflow and decision automation that brings business users and software developers together.

[GEMMA Zaken](https://github.com/sjefvanleeuwen/gemma-zaken)  
OpenAPI .NET Core test implementations for ZDS / Zaakgericht werken

[SIGNALR Event Hub](https://github.com/sjefvanleeuwen/signalr-event-hub)  
Generic event hub mainly for front end signalling if an asycnhronous task has completed in the backend 

[BRP Basis Registratie Personen](https://github.com/sjefvanleeuwen/openapi-brp-fake)  
Provides an OpenAPI service that serves a fake BRP (Basisregistratie Personen) implementation. The implementation will generate fake addresses, names based on random seed derived from a BSN (Burger Service Nummer / Social Security Number).

[OpenXML Document Generator](https://github.com/sjefvanleeuwen/openxml-document-generator)  
Generates documents from html into openxml standard.

## 5Layer Composition

```yaml
version: '3.4'

# 5Layer reference architecture and service implementations.
services:
  # GEMMA ZDS Document Registratie OpenAPI
  drc:
    image: wigo4it/openapi-drc:alpha
    hostname: zdc
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
  signalr-event-hub:
    image: wigo4it/signalr-event-hub
    hostname: signalr-event-hub
    ports:
      - "5051:5051"
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