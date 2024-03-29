# Togglr Spring Boot Client Library


## How to Use the Togglr Client

### Add the Togglr Client as a dependency

TODO:  Get an artifact published to include through Maven or Gradle.

### Configure Spring

In your main class, add the following annotation:
```java
@ComponentScan( basePackages = {"com.heb.my-spring-app", "com.heb.togglr"})
public class MySpringApplication {

    public static void main(String[] args) {
        SpringApplication.run(AuthServiceApplication.class, args);
    }

}
```

If your application is under the com.heb package, you can set it to `com.heb`.


### Properties
Add the following properties to your Spring Application (or configure them through Environment Variables):

`heb.togglr.client.app-id`  is the application Id within your Togglr server.

`heb.togglr.client.server-url`  is the endpoint URL of your Togglr server.

`heb.togglr.cache-time` is the amount of time you want Togglr features to be cached for. (Updates to Togglr will cause a cache refresh regardless of this value being set)

`heb.togglr.client.cache-type` Value can be either `redis` or `in-memory`

If the `cache-type` is set to `redis` you'll need to include the following properties:

`heb.togglr.redis.host` Host for Redis

`heb.togglr.redis.port` Port for Redis

You can get your application ID from the Togglr Server.


### Integrate Your Code

Where you need to get the Togglr configurations for users (generally in your `AuthenticationSuccessHandler`) auto-wire
the Togglr Client: 

```java

    private TogglrClient togglrClient;

    public RestAuthSuccessHandler(JwtService jwtService, TogglrClient togglrClient){
        this.jwtService = jwtService;
        this.togglrClient = togglrClient;
    }
```

To get the Active Featuers for a user, build an ActiveFeatureRequest and make the call to the Togglr Client.
You *do not* have to set the `applicationId` in the ActiveFeatureRequest, it will be set by the client.

```java
        ActiveFeaturesRequest featuresRequest = new ActiveFeaturesRequest();
        featuresRequest.getConfigs().put("user", this.username);

        List<GrantedAuthority> roles = this.togglrClient.getFeaturesForConfig(featuresRequest, this.username);
```

The roles you are returned can then be added to your UserDetails.

The call to `this.togglrClient.getFeaturesForConfig()` accepts two parameters. The first is the ActiveFeatureRequest.
The second value is the identifier for the user, and is only used to cache values.

For example, if you wanted to configure your server to have logic depending on if you had a feature set, you could call 

```java
this.togglrClient.getFeaturesForConfig(featuresRequest, "system")
```

## Building From Source

If you wish to build the library from source, it can be compiled into a jar with the following command:

```
mvn org.apache.maven.plugins:maven-jar-plugin:3.0.2:jar
```