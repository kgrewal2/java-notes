# Key Components

## Controller

`@RestController` can be used to create controllers for `Get`, `Post`, `Put`, and `Delete` operations. Rest Controller contains instance for repository and use this instance for accessing database.

See `@ControllerAdvice` to know more about adding response status with the response body. `curl -v` can be used to get response statuses.

In addition to the data fetching and manipulation functions, REST implies having Hypermedia Links to navigate through the API. These links can include the links to fetch the other relevant data. See HATEOS and `RepresentationModelAssembler<Classname, EntityModel>` for more details.

## Model

`@Entity` is used to configure Spring Application objects. `@Id` and `@GeneratedValue` annotations can be used for the `id` variable. `equals`, `hashCode`, and `toString` methods are also overridden using IntelliJ's Generate functionality.

## Configuration

`@Configuration` can be used to configure the Spring Application. This component can contain the `CommandLineRunner` to load the data into the Spring Database.

## Repository

Repository class for each model class extends `JpaRepository<ModelName, IdType(Generally Long)>`.
