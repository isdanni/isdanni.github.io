---
layout: single
title: "GCP Cloud SQL integration w/ Java Web App"
author: "Danni"
toc: true
toc_sticky: true
tags:
  - Database
  - Web
---

> MyBatis + Spring Boot: Servlet, JSP & JDBC

There are some well-known frameworks for developing the non-distributed Java web apps:
- **ORM**: Ususally for controlling the relational database(data access w\ JDBC), right now SQL-based `MyBatis` or `Hibernate` is pretty popular.
- **MVC**: Model, View and Controller. Each component interacts with one another instead of replying on each other. This design pattern allows code reuse and parallel development.
- **Spring**: extensive features that work either for any Java apps or Java EE. Spring AOP can help everyone reduce the burden of creating the object, manaing the exceptions, recording the logs & more.

I've worked with most of these functions with the most common databases: MySQL, PostgreSQL... but never with GCP Cloud SQL. So it's pretty fun to try it for the first time and see how everthing works. This post simply serves as a recording function.

### How to

###### Create the MySQL Instance for CloudSQL

Makre sure you have all the necessary APIs enabled. In the command line type to checl the connection name:

`gcloud sql instances describe <INSTANCE_NAME> | grep connectionName`.

###### Add dependencies in `Pom.xml`

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>6.0.5</version>
</dependency>

<dependency>
    <groupId>com.google.cloud.sql</groupId>
    <artifactId>mysql-socket-factory-connector-j-6</artifactId>
    <version>1.0.14</version>
</dependency>
```

###### Add config.properties in src/main/resources
```shell
# [START gae_flex_mysql_config_properties]
sqlUrl=${sqlURL}
# [END gae_flex_mysql_config_properties]
```
###### Add test servlet to test connections

Add a socket factory Java file under the `src/main/package...`, here is one example file taken from [here]():

As you can see, the program stores some SQL command as strings and try test connections by creating the databse & insert data into it.

If the connection fails, or there are some other errors, it throws a specific error.

```java
/*
 * Copyright 2018 Google LLC
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 * http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package com.google.cloud.sql.mysql;

import com.google.cloud.sql.core.CoreSocketFactory;
import com.mysql.cj.conf.PropertySet;
import com.mysql.cj.protocol.ServerSession;
import com.mysql.cj.protocol.SocketConnection;
import java.io.Closeable;
import java.io.IOException;
import java.util.Properties;

/**
 * A MySQL {@link SocketFactory} that establishes a secure connection to a Cloud SQL instance using
 * ephemeral certificates.
 *
 * <p>The heavy lifting is done by the singleton {@link CoreSocketFactory} class.
 */
public class SocketFactory implements com.mysql.cj.protocol.SocketFactory {

  @Override
  public <T extends Closeable> T connect(
      String host, int portNumber, PropertySet props, int loginTimeout) throws IOException {
    return connect(host, portNumber, props.exposeAsProperties(), loginTimeout);
  }

  /**
   * Implements the interface for com.mysql.cj.protocol.SocketFactory for mysql-connector-java prior
   * to version 8.0.13. This change is required for backwards compatibility.
   */
  public <T extends Closeable> T connect(
      String host, int portNumber, Properties props, int loginTimeout) throws IOException {
    @SuppressWarnings("unchecked")
    T socket = (T) CoreSocketFactory.connect(props, CoreSocketFactory.MYSQL_SOCKET_FILE_FORMAT);
    return socket;
  }

  // Cloud SQL sockets always use TLS and the socket returned by connect above is already TLS-ready.
  // It is fine to implement these as no-ops.
  @Override
  public void beforeHandshake() {}

  @Override
  public <T extends Closeable> T performTlsHandshake(
      SocketConnection socketConnection, ServerSession serverSession) throws IOException {
    @SuppressWarnings("unchecked")
    T socket = (T) socketConnection.getMysqlSocket();
    return socket;
  }

  @Override
  public void afterHandshake() {}
}
```
Then follow the following steps:
- First run `mvn jetty:run`. This command allows us to build and run the web application without needing to assemble it into a war by using the jetty-maven-pluging;
- Run with `mvn clean`, then `mvn appengine:devserver`;
- Check local host in the browser and see if there throws any error.

### Reference
[1] [Cloud SQL Socket Factory for JDBC drivers - GitHub](https://github.com/GoogleCloudPlatform/cloud-sql-jdbc-socket-factory)

[2] [gcloud command-line tool overview](https://cloud.google.com/sdk/gcloud/)

[3] [connector - 8](https://github.com/GoogleCloudPlatform/cloud-sql-jdbc-socket-factory/tree/master/connector-j-8)

[4] [Simple JDBC comparision](https://github.com/codingXiaxw/JavaWeb-Regist-and-Login/blob/master/LoginRegist2/src/dbconfig.properties)

[5] [cloudsql connection pool checking](https://github.com/GoogleCloudPlatform/java-docs-samples/tree/master/cloud-sql/mysql/servlet/src/main/java/com/example/cloudsql)