# Deploy a JavaEE app to Cloud Foundry

This project shows how to deploy a JavaEE app to Cloud Foundry, using two options:
  1. [Java Buildpack](https://github.com/cloudfoundry/java-buildpack) (leveraging Apache Tomcat)
  2. [IBM WebSphere Application Server Liberty Buildpack](https://github.com/cloudfoundry/ibm-websphere-liberty-buildpack)

Yes, that's true: you **can** [deploy a JavaEE application server to Cloud Foundry](https://content.pivotal.io/pivotal-blog/new-tools-from-pivotal-ibm-pave-the-way-for-java-ee-websphere-apps-to-move-to-cloud-foundry-and-kubernetes)!

## How to use it?

Compile this project using a JDK 8:
```bash
$ ./mvnw clean package
```

## Run this app locally

Deploy this app on your workstation using Apache Tomcat:
```bash
$ ./mvnw tomcat7:run
```

This app provides a single `HttpServlet` displaying basic informations:
```bash
$ curl http://localhost:8080
Hello JavaEE
Java version: 11.0.1
Java vendor: Oracle Corporation
Server info: Apache Tomcat/7.0.47
```

## Deploy to Cloud Foundry using Java Buildpack

By pushing a WAR file to Cloud Foundry, the Java Buildpack will automatically
deploy this artifact using an Apache Tomcat server.

Just push this application:
```bash
$ cf push
```

A random URL is assigned to the app. Hit this endpoint to display basic
informations:
```bash
$ curl http://cf-hellojavaee-silly-kob.apps.pas.pvtl.eu
Hello JavaEE
Java version: 1.8.0_192
Java vendor: Oracle Corporation
Server info: Apache Tomcat/8.5.34
```

## Deploy to Cloud Foundry using WebSphere

The IBM WebSphere Application Server Liberty Buildpack allows you to
deploy JavaEE apps to Cloud Foundry, while still supporting what makes
Cloud Foundry so great for developers (on-demand services,
auto scaling, etc.).

Deploying a JavaEE app to Cloud Foundry requires a single command:
```bash
$ cf push -f manifest-liberty.yml
```

This manifest just defines the buildpack to use as well as some license codes:
```yaml
---
applications:
  - name: cf-hellojavaee
    path: target/cf-hellojavaee.war
    random-route: true
    buildpacks:
      - https://github.com/cloudfoundry/ibm-websphere-liberty-buildpack.git
    env:
      IBM_JVM_LICENSE: "L-SMKR-AVSEUH"
      IBM_LIBERTY_LICENSE: "L-CTUR-B4WNHE"
```

Your app will be deployed to Cloud Foundry in no time, using a full blown
JavaEE application server:
```bash
$ curl http://cf-hellojavaee-daring-springhare.apps.pas.pvtl.eu
Hello WAR
Java version: 1.8.0_191
Java vendor: IBM Corporation
Server info: IBM WebSphere Liberty/19.0.0.1
```

If you're using [Pivotal Cloud Foundry](https://pivotal.io/platform/pivotal-application-service),
please note this buildpack is
also available as a supported product on
[Pivotal Network](https://network.pivotal.io/products/ibm-websphere-liberty).

## Contribute

Contributions are always welcome!

Feel free to open issues & send PR.

## License

Copyright &copy; 2019 [Pivotal Software, Inc.](https:/pivotal.io)

This project is licensed under the [Apache Software License version 2.0](https://www.apache.org/licenses/LICENSE-2.0).
