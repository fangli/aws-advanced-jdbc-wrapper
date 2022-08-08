# The JDBC Wrapper Integration with 3rd Party Frameworks

## Hibernate

You can configure database access using an XML configuration file `hibernate.cfg.xml`.
If you use Hibernate with a connection pool, please check a page corresponding to a particular pool on suggested configuration.
```hibernate.cfg.xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE hibernate-configuration PUBLIC "-//Hibernate/Hibernate Configuration DTD 3.0//EN" "http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <property name="hibernate.connection.driver_class">com.amazon.awslabs.jdbc.Driver</property>
        <property name="hibernate.connection.url">aws-jdbc-wrapper:postgresql://localhost/mydatabase</property>
        <property name="hibernate.connection.username">myuser</property>
        <property name="hibernate.connection.password">secret</property>
    </session-factory>
</hibernate-configuration>
```

### References
- [Hibernate ORM](https://hibernate.org/orm/)
- [The Hibernate configuration file](https://docs.jboss.org/hibernate/orm/current/quickstart/html_single/#hibernate-gsg-tutorial-basic-config)

## Spring Framework

You can use the following sample code as a reference to configure DB access in your application.

```SpringJdbcConfig.java
@Configuration
@ComponentScan("com.myapp")
public class SpringJdbcConfig {
    @Bean
    public DataSource mysqlDataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("com.amazon.awslabs.jdbc.Driver");
        dataSource.setUrl("jdbc:postgresql://localhost:5432/testDatabase");
        dataSource.setUsername("guest_user");
        dataSource.setPassword("guest_password");

        return dataSource;
    }
}
```