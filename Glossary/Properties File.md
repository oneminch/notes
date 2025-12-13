- In the context of [[Java]] programs, a properties file is a simple text file used to store configuration data and settings in a key-value pair format.
- They have a `.properties` file extension.
- In Java, they can be interacted with using the `java.util.Properties` class.

```
DB_URL=jdbc:postgresql://localhost:5432/db
CONN_USERNAME=user
CONN_PASSWORD=password
```

```java
Properties prop = new Properties();
FileInputStream input = new FileInputStream("config.properties");
prop.load(input);
String dbUrl = prop.getProperty("DB_URL");
```

- For a more secure workflow, we would
    1. create environment variables that contain the information our application will need.
    2. create a properties file where the values are the names of our environment variables
    3. extract the credentials from our system using the properties file in the data access class.

```java
FileInputStream fileStream = new FileInputStream("config.properties");

Properties prop = new Properties();
prop.load(fileStream);

String url = properties.getProperty("DB_URL");
String username = properties.getProperty("CONN_USERNAME");
String password = properties.getProperty("CONN_PASSWORD");

URL = System.getenv(url);
CONNECTION_PASSWORD = System.getenv(password);
CONNECTION_USERNAME = System.getenv(username);
```
