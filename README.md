# Panda-Application
A little bit more advanced Web application with Managed Beans, Servlets WebFilters, JSF and Hibernate, deployed on TomEE.
___
PANDA (Package Acceptance and National Delivery Application) is a platform for package deliveries, which is a fast-rising Start-Up, which lacks a web application. You have been employed by the KFC (Kung-Fu-Chicken) Corporation to implement a web platform for PANDA. 
___
## Setup
Tested on Windows 8 x64
### Versions
* Java **11.0.2**
* Maven **3.6.0**
* Maven Compiler **3.8.0**
* MySQL with mysql-connector-java **8.0.15**
* hibernate-core **5.4.1.Final**
* Apache TomEE 8.0.0.M2 **webprofile**
### Other tools:
* [ModelMapper](http://modelmapper.org/)
* [Jargon2](https://github.com/kosprov/jargon2-api) - Fluent Java API for Argon2 password hashing
### Environment configuration
System and IDE should be configured to use:
* [Java **11.0.2**](https://docs.oracle.com/cd/E19509-01/820-3208/inst_cli_jdk_javahome_t/) - [IntelliJ](https://stackoverflow.com/questions/18987228/how-do-i-change-the-intellij-idea-default-jdk)
* [Maven **3.6.0**](http://maven.apache.org/install.html) - [IntelliJ](https://www.jetbrains.com/help/idea/maven-support.html#create_new_maven_project)
* [mysql-connector-java **8.0.15**](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-installing-classpath.html) - [IntelliJ](https://www.jetbrains.com/help/idea/connecting-to-a-database.html)

This files should be present in **TomEE\lib** folder 
* [hibernate-core-5.4.1.Final.jar](http://central.maven.org/maven2/org/hibernate/hibernate-core/5.4.1.Final/hibernate-core-5.4.1.Final.jar)
* [hibernate-jpa-2.1-api-1.0.2.Final.jar](http://central.maven.org/maven2/org/hibernate/javax/persistence/hibernate-jpa-2.1-api/1.0.2.Final/hibernate-jpa-2.1-api-1.0.2.Final.jar)
___
#### Project configuration
* [pom.xml](https://github.com/ivelin1936/Panda-Application/blob/master/pom.xml) - project setup - dependencies, compile, packaging
* [beans.xml](https://github.com/ivelin1936/Panda-Application/blob/master/src/main/webapp/WEB-INF/beans.xml) - default setup with **bean-discovery-mode="all"**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
        xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee 
                      http://xmlns.jcp.org/xml/ns/javaee/beans_1_1.xsd"
        bean-discovery-mode="all">
</beans>
```
* [web.xml](https://github.com/ivelin1936/Panda-Application/blob/master/src/main/webapp/WEB-INF/web.xml)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         id="WebApp_ID" version="4.0">

    <!-- Welcome page -->
    <welcome-file-list>
        <welcome-file>faces/view/index.xhtml</welcome-file>
    </welcome-file-list>

    <!-- JSF mapping -->
    <servlet>
        <servlet-name>Faces Servlet</servlet-name>
        <servlet-class>javax.faces.webapp.FacesServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!-- Map these files with JSF -->
    <servlet-mapping>
        <servlet-name>Faces Servlet</servlet-name>
        <url-pattern>*.xhtml</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>Faces Servlet</servlet-name>
        <url-pattern>*.jsf</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>Faces Servlet</servlet-name>
        <url-pattern>*.faces</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>Faces Servlet</servlet-name>
        <url-pattern>/faces/*</url-pattern>
    </servlet-mapping>
</web-app>
```
* Using transaction-type="RESOURCE_LOCAL"
* [persistence.xml](https://github.com/ivelin1936/Panda-Application/blob/master/src/main/resources/META-INF/persistence.xml) - persistence unit setup
```html
<?xml version="1.0" encoding="UTF-8"?>
<persistence xmlns="http://java.sun.com/xml/ns/persistence"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://java.sun.com/xml/ns/persistence
             http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd"
             version="2.0">

    <persistence-unit name="pandaPU" transaction-type="RESOURCE_LOCAL">
        <properties>
            <property name="hibernate.connection.url" value="jdbc:mysql://localhost:3306/panda_db?createDatabaseIfNotExist=true&amp;useSSL=false&amp;serverTimezone=UTC" />
            <property name="hibernate.connection.driver_class" value="com.mysql.jdbc.Driver" />
            <property name="hibernate.hbm2ddl.auto" value="update"/>
            <property name="hibernate.connection.username" value="root"/>
            <property name="hibernate.connection.password" value="1234"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.MySQL5Dialect"/>
            <property name="hibernate.show_sql" value="true" />
            <property name="hibernate.format_sql" value="true" />
        </properties>
    </persistence-unit>
</persistence>
```
___
* Creating beans of external classes by **@Produces** annotation
[Example](https://github.com/ivelin1936/Panda-Application/blob/master/src/main/java/pandaApp/Config/ApplicationBeanConfiguration.java)
```java
public class ApplicationBeanConfiguration {

    @Produces
    public ModelMapper modelMapper() {
        return new ModelMapper();
    }

    @Produces
    public EntityManager entityManager() {
        return Persistence.createEntityManagerFactory(AppConstants.PERSISTENCE_UNIT)
                .createEntityManager();
    }
}
```
* Use of [Jargon2](https://github.com/kosprov/jargon2-api) for password hashing

[Configuration](https://github.com/ivelin1936/Panda-Application/blob/master/src/main/java/pandaApp/utils/PasswordHasher.java) / 
[Usage](https://github.com/ivelin1936/Panda-Application/blob/master/src/main/java/pandaApp/service/userService/UserServiceImpl.java)
```java
public class UserServiceImpl implements UserService {

    private final UserRepository userRepository;
    private final ModelMapper modelMapper;
    private final PasswordHasher passwordHasher;
    
    // ...
    
     @Override
    public void register(UserServiceModel serviceModel) {
        User user = this.modelMapper.map(serviceModel, User.class);
        String encodedHash = passwordHasher.encodedHash(serviceModel.getPassword().toCharArray());
        user.setPassword(encodedHash);
        this.setUserRole(user);

        this.userRepository.save(user);
    }
    
    @Override
    public UserServiceModel login(UserServiceModel serviceModel) {
        User user = userRepository.findByUsername(serviceModel.getUsername());

        char[] passwordCharArr = serviceModel.getPassword().toCharArray();
        if (user == null || !passwordHasher.verifyEncoded(user.getPassword(), passwordCharArr)) {
            return null;
        }

        return this.modelMapper.map(user, UserServiceModel.class);
    }
    //...
```
___

## Project Functionality
##### The application provide Guest (not logged in) users with the functionality to:
* Login 
* Register
* View the Guest Index page
##### The application provide Users (logged in) with the functionality to:
* Logout
* View their Packages
* View details about a Package
* View their Receipts
* View details about a Receipt
##### The application provide Admins (logged in, with role - Admin) with the functionality to:
* Logout
* View their Packages
* View details about a Package
* View their Receipts
* View details about a Receipt
* View all Pending Packages
* View all Shipped Packages
* View all Delivered Packages
* View details about all Delivered Packages
* Ship Packages
* Deliver Packages

___
## Technologies
* Java - [JDK11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

## Built With
* [Maven](https://maven.apache.org/) - Dependency Management

## Version
1.0-SNAPSHOT

## Author
**Ivelin DImitrov** 
* [GitHub](https://github.com/ivelin1936)
* [LinkedIn](https://www.linkedin.com/in/ivelin-dimitrov-42b13a151/)

## License
This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details
