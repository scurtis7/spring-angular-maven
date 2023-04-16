# spring-angular-maven

These are my notes for creating a Spring Boot and Angular application with Maven using the frontend-maven-plugin.

## Description

We will create a maven project that contains two modules. A server backend module and an angular client module.  We will use the frontend-maven-plugin to build and copy the frontend to the build.

### Prerequisites

- Java - 17
- Spring Boot - 3.0.5
- Angular - 15.2.6

Let's get started.

## Create Root Project Folder

Create your root project folder: `spring-angular-maven`

Next add a `pom.xml` and if you want to add a `README.md` file.

Your pom file should be similar to the following:
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.0.5</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.scurtis</groupId>
    <artifactId>spring-angular-maven</artifactId>
    <version>0.0.1</version>
    <name>spring-angular-maven</name>
    <description>Spring Boot + Angular + Maven project</description>

    <properties>
        <java.version>17</java.version>
    </properties>
    <packaging>pom</packaging>

    <modules>
        <module>client</module>
        <module>server</module>
    </modules>

</project>
```

You will want to modify the following:
- Change the version of spring boot to the latest
- Modify the artifactId to match your project name
- Modify the name and description tags as well
- We will update the README and .gitignore later

## Create Spring Boot App (name it server)

Use the Spring Initializer to create the server project and enter details similar to below:

- Project -> Maven
- Language -> Java
- Spring Boot -> 3.0.5
- Artifact -> server
- Packaging -> Jar
- Java -> 17
- Dependencies
  - Lombok
  - Spring Web

Once you have made the selections above:

- Click generate and save the zip file in the folder you created above.
- Unzip the file and then delete the zip file
- This is a multi-module project so we need to modify the pom file to something like below
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.scurtis</groupId>
        <artifactId>spring-angular-maven</artifactId>
        <version>0.0.1</version>
    </parent>

    <artifactId>server</artifactId>
    <version>0.0.1</version>

    <dependencies>
        <dependency>
            <groupId>com.scurtis</groupId>
            <artifactId>client</artifactId>
            <version>${project.version}</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <artifactId>maven-resources-plugin</artifactId>
                <executions>
                    <execution>
                        <id>copy-resources</id>
                        <phase>validate</phase>
                        <goals>
                            <goal>copy-resources</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>${project.build.directory}/classes/resources/</outputDirectory>
                            <resources>
                                <resource>
                                    <directory>${project.parent.basedir}/client/dist/client</directory>
                                </resource>
                            </resources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-failsafe-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```
I like yaml better than properties files, so I like to rename `server/src/main/resources/application.properties` to `application.yml` and configure it as below:
```
# application properties

logging:
  level:
    root: INFO
    com:
      scurtis: INFO
```
I also like to create a local properties to contain my sensitive configurations and add it to `.gitignore`, so it's not checked in.  Copy `application.yml` into a new file called `application-local.yml` and configure it as below:
```
# application-local properties

server:
  port: 8080

logging:
  level:
    root: INFO
    com:
      scurtis: TRACE
```

## Server Cleanup Tasks

- Move the `.gitignore` file from the server folder to the root folder `spring-angular-maven/server` -> `spring-angular-maven`
- Delete the following files from the spring-angular-maven/server folder
    - HELP.md
    - mvnw
    - mvnw.cmd
- Delete the `spring-angular-maven/server/.mvnfolder`


## Create Angular App

We will now create the Angular application.

Open a terminal and navigate to the root directory `spring-angular-maven` and run the following command
```
ng new client
```

- Answer y to routing
- I like to select SASS for styling, but you can choose what your comfortable with
- This will create the angular application in the new client folder
    - `NOTE: You will want to delete the .git folder and copy the contents of the .gitignore to the other .gitignore and then delete it.  This is because you want to initialize git in the root folder.  There is some duplication in the .gitignore files so we will update that later.`
- We can run the Angular app just change to the client folder and run the following command:
```
ng serve --open
```

This is just to verify everything is created correctly and will start the application and open the web page similar to below.

- Press Ctrl + C to stop the server


### Configure pom File For Client

First add a `pom.xml` file to the client folder and configure it similar to below
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>com.scurtis</groupId>
    <artifactId>spring-angular-maven</artifactId>
    <version>0.0.1</version>
  </parent>

  <artifactId>client</artifactId>
  <version>0.0.1</version>

  <build>
    <resources>
      <resource>
        <directory>./dist/client</directory>
        <targetPath>static</targetPath>
      </resource>
    </resources>
    <plugins>
      <plugin>
        <groupId>com.github.eirslett</groupId>
        <artifactId>frontend-maven-plugin</artifactId>
        <version>1.12.1</version>
        <configuration>
          <workingDirectory>./</workingDirectory>
          <nodeVersion>v19.9.0</nodeVersion>
          <npmVersion>9.6.3</npmVersion>
        </configuration>
        <executions>
          <execution>
            <id>install node and npm</id>
            <goals>
              <goal>install-node-and-npm</goal>
            </goals>
          </execution>
          <execution>
            <id>npm install</id>
            <goals>
              <goal>npm</goal>
            </goals>
          </execution>
          <execution>
            <id>npm run build</id>
            <goals>
              <goal>npm</goal>
            </goals>
            <configuration>
              <arguments>run build</arguments>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>

</project>
```

You will need to change the nodeVersion and npmVersion in the `pom.xml` file above.

Run the following commands to determine the versions

- `node --version`
- `npm --version`

You can also change the version of the frontend-maven-plugin by checking the Maven repo for the latest version here:

https://mvnrepository.com/artifact/com.github.eirslett/frontend-maven-plugin


## Create IDE Project

This is where I like to set up my project in an IDE.  I like IntelliJ, but you can use whatever IDE you like.  For IntelliJ I open IntelliJ and create a new project by opening the pom file in the root folder.

## Configure Angular To Handle Routes

Spring Boot will try to handle requests by default, but we want Angular to handle requests.  In order to do that we need to update the server project and add a config package and then add WebConfig class that implements the WebMvcConfigurer interface.  The class should look something like below.

```
package com.scurtis.server.config;

import java.io.IOException;
import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.Resource;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import org.springframework.web.servlet.resource.PathResourceResolver;

public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {

        registry.addResourceHandler("/**")
                .addResourceLocations("classpath:/static/")
                .resourceChain(true)
                .addResolver(new PathResourceResolver() {
                    @Override
                    protected Resource getResource(String resourcePath, Resource location) throws IOException {
                        Resource requestedResource = location.createRelative(resourcePath);
                        return requestedResource.exists() && requestedResource.isReadable() ? requestedResource
                                : new ClassPathResource("/static/index.html");
                    }
                });
    }

}
```


## Configure git Repo

Now would be a good time to initialize the git repo and commit your changes.

Make sure to delete the .git folder in the client and server folder.  You should have already copied the contents of .gitignore in client to the .gitignore in the root folder and deleted it.  If not, do that now.

Make sure your .gitignore is the way you want it.  Here is a sample:
```
target/
!.mvn/wrapper/maven-wrapper.jar
!**/src/main/**
!**/src/test/**

### IntelliJ IDEA ###
.idea
*.iws
*.iml
*.ipr

### Maven
.mvn/
mvnw
mvnw.cmd

### Node
client/dist
client/node_modules
client/node/
client/package-lock.json

# profiling files
chrome-profiler-events*.json

# IDEs and editors
/.idea
.project
.classpath
.c9/
*.launch
.settings/
*.sublime-workspace

# misc
/.sass-cache
/connect.lock
/coverage
/libpeerconnection.log
npm-debug.log
yarn-error.log
testem.log
/typings

# System Files
.DS_Store
Thumbs.db

# Miscellaneous
server/src/main/resources/application-local.yml
```

Change back to the root folder (spring-angular-maven) first.
```
git init
git add .
git commit -m 'initial commit'
```


## Build And Run Application

- Open a terminal and navigate to the root folder
- Run the following command to build the application
  `mvn clean package`
- Now you can run the application with the following command
  `java -jar server/target/server-0.0.1.jar`
- Open a browser and navigate to here:  http://localhost:8080


## Next Steps

Now you have the shell of an application.  You can create the web application and backend server code to support it.
