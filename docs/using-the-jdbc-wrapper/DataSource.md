# Connecting with a DataSource
In addition to the `DriverManager` class, one can also use a datasource to establish new connections.
The JDBC Wrapper has a built-in datasource class called [AwsWrapperDataSource](../../wrapper/src/main/java/com/amazon/awslabs/jdbc/ds/AwsWrapperDataSource.java).
The `AwsWrapperDataSource` enables the JDBC Wrapper to work with various driver-specific datasources.

## Using the AwsWrapperDataSource

There are 3 tasks to establish a connection with the AwsWrapperDataSource:

1. Configure the property names for the underlying driver-specific datasource. 
2. Target driver-specific datasource.
3. Configure the driver-specific datasource.

### Configurable DataSource Properties

To allow the AWS Advanced JDBC Wrapper to work with multiple driver-specific datasources,
it is necessary for the user to specify what property names the underlying datasource uses.
For example, one datasource implementation could use the method `setUser` to set the datasource username,
and another could use the method `setUsername` to do the same.
See the table below for a list of configurable property names.

> **:warning: Note:** If the same connection property is provided both explicitly in the connection URL and in the datasource properties, the value set in the connection URL will take precedence. 

| Property                    | Configuration Method           | Description                                                                                    | Type     | Required                                                                                              | Example                                |
|-----------------------------|--------------------------------|------------------------------------------------------------------------------------------------|----------|-------------------------------------------------------------------------------------------------------|----------------------------------------|
| Server name                 | `setServerPropertyName`        | The name of the server or host property.                                                       | `String` | Yes, if no URL is provided.                                                                           | `serverName`                           |
| Database name               | `setDatabasePropertyName`      | The name of the database property.                                                             | `String` | No                                                                                                    | `databaseName`                         |
| Port name                   | `setPortPropertyName`          | The name of the port property.                                                                 | `String` | No                                                                                                    | `port`                                 |
| URL name                    | `setUrlPropertyName`           | The name of the URL property.                                                                  | `String` | No                                                                                                    | `url`                                  |
| User name                   | `setUserPropertyName`          | The name of the username property.                                                             | `String` | No                                                                                                    | `user`                                 |
| Password name               | `setPasswordPropertyName`      | The name of the password property.                                                             | `String` | No                                                                                                    | `password`                             |
| JDBC URL                    | `setJdbcUrl`                   | The URL you would like to connect with.                                                        | `String` | No, if there is enough information provided by the other properties that can be used to create a URL. | `jdbc:postgresql://localhost/postgres` |
| JDBC protocol               | `setJdbcProtocol`              | The JDBC protocol that will be used.                                                           | `String` | Yes                                                                                                   | `jdbc:postgresql:`                     |
| Underlying DataSource class | `setTargetDataSourceClassName` | The fully qualified class name of the underlying DataSource class the JDBC Wrapper should use. | `String` | Yes, if the JDBC URL has not been set.                                                                | `org.postgresql.ds.PGSimpleDataSource` |

## Using the AwsWrapperDataSource with Connection Pooling Frameworks

The JDBC Wrapper also supports establishing connection with a connection pooling framework.

There are 4 tasks to use the JDBC Wrapper with a connection pool:

1. Configure the connection pool.
2. Set the datasource class name to `com.amazon.awslabs.jdbc.ds.AwsWrapperDataSource` for the connection pool.
3. Configure the `AwsWrapperDataSource`.
4. Configure the driver-specific datasource.

### HikariCP
[HikariCP](https://github.com/brettwooldridge/HikariCP) is a popular connection pool, below are the steps to a simple connection pool with HikariCP and PostgreSQL:

1. Configure the HikariCP datasource:
   ```java
   HikariDataSource ds = new HikariDataSource();
   
   // Configure the connection pool:
   ds.setMaximumPoolSize(5);
   ds.setIdleTimeout(60000);
   ds.setUsername(USER);
   ds.setPassword(PASSWORD);
   ```

2. Set the datasource class name to `com.amazon.awslabs.jdbc.ds.AwsWrapperDataSource`:
   ```java
   ds.setDataSourceClassName(AwsWrapperDataSource.class.getName());
   ```

3. Configure the `AwsWrapperDataSource`:
   ```java
   // Note: jdbcProtocol is required when connecting via server name
   ds.addDataSourceProperty("jdbcProtocol", "jdbc:postgresql:");
   ds.addDataSourceProperty("serverPropertyName", "serverName");
   
   ds.addDataSourceProperty("userPropertyName", "user");
   ds.addDataSourceProperty("passwordPropertyName", "password");
   ds.addDataSourceProperty("databasePropertyName", "databaseName");
   ds.addDataSourceProperty("portPropertyName", "port");
   ```

4. Configure the driver-specific datasource:
   ```java
   ds.addDataSourceProperty("targetDataSourceClassName", "org.postgresql.ds.PGSimpleDataSource");
   
   Properties targetDataSourceProps = new Properties();
   targetDataSourceProps.setProperty("serverName", "db-identifier.cluster-XYZ.us-east-2.rds.amazonaws.com");
   targetDataSourceProps.setProperty("databaseName", "postgres");
   targetDataSourceProps.setProperty("port", "5432");
   ```

See [here](./sample-code/HikariSample.java) for a complete sample.

> **:warning:Note:** HikariCP supports either DataSource-based configuration or DriverManager-based configuration by specifying the `dataSourceClassName` or the `jdbcUrl`. When using the `AwsWrapperDataSource` one must specify the `dataSourceClassName`, therefore `HikariDataSource.setJdbcUrl` is not supported. For more information see HikariCP's [documentation](https://github.com/brettwooldridge/HikariCP#gear-configuration-knobs-baby).