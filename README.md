# Deploy a JavaEE app to Cloud Foundry

This project shows how to
[deploy a JavaEE app to Cloud Foundry](https://content.pivotal.io/pivotal-blog/new-tools-from-pivotal-ibm-pave-the-way-for-java-ee-websphere-apps-to-move-to-cloud-foundry-and-kubernetes),
using the [IBM WebSphere Application Server Liberty Buildpack](https://github.com/cloudfoundry/ibm-websphere-liberty-buildpack).

## How to use it?

Compile this project using a JDK 8:
```bash
$ ./mvnw clean package
```

## Run this app locally

Deploy this app on your workstation using any JEE application server, such as
[Wildfly](https://wildfly.org). Just deploy the file `cf-hellojavaee.ear` to
your application server.

This app provides a single `HttpServlet` displaying basic informations:
```bash
$ curl http://localhost:8080
Hello JavaEE!
Java version: 1.8.0_181
Java vendor: Oracle Corporation
Server info: WildFly Full 15.0.1.Final (WildFly Core 7.0.0.Final) - 2.0.15.Final
```

The greetings message is retrieved using the stateless EJB service
`GreetingService`:
```java
@Stateless
public class GreetingService {
    public String greetings(String who) {
        return "Hello " + who + "!";
    }
}
```

The servlet code is that simple:
```java
@WebServlet(urlPatterns = "/")
public class IndexServlet extends HttpServlet {
    @EJB
    private GreetingService greetingService;

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException {
        final String message = greetingService.greetings("JavaEE");

        final String javaVersion = System.getProperty("java.version");
        final String javaVendor = System.getProperty("java.vendor");
        final String serverInfo = getServletContext().getServerInfo();

        resp.setContentType("text/plain");
        try (final PrintWriter out = resp.getWriter()) {
            out.write(message + "\nJava version: "
                    + javaVersion + "\nJava vendor: "
                    + javaVendor + "\nServer info: "
                    + serverInfo + "\n");
        }
    }
}
```

## Run this app on Cloud Foundry

The IBM WebSphere Application Server Liberty Buildpack allows you to
deploy JavaEE apps to Cloud Foundry, while still supporting what makes
Cloud Foundry so great for developers (on-demand services,
auto scaling, etc.).

Deploying a JavaEE app to Cloud Foundry requires a single command:
```bash
$ cf push
```

This manifest just defines the buildpack to use:
```yaml
---
applications:
  - name: cf-hellojavaee
    path: ear/target/cf-hellojavaee.ear
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
Hello JavaEE!
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
