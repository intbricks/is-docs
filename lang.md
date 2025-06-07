## Configuration Language

Integration Service (IS) configuration language is based on [HCL](https://github.com/hashicorp/hcl). Key values include:
- Less verbose compared to JSON, XML as a domain-specific language.
- Requires minimum on-boarding time for users (system admins, developers, data-warehouse developers, 
 ETL developers, integration developers...).
- Users will be able to maintain IS configuration files along with other infrastructure and application source code in
  the same version control repository.

### Concepts

IS configuration consists of four main entities, `connection`, `connector`, `schedule`, `service`.
Each configuration file should start with `version` and `package`

| Entity       | Description                                                                                                                              | Required                                 | 
|--------------|------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------|
| `version`    | IS configuration langauge version.                                                                                                       | Yes. Current langauge version is `1.0.0` |
| `package`    | A package describes a collection of configuration files.                                                                                 | Yes.                                     |
| `connection` | Represents a connection to an external resource such as HTTP endpoint, database.                                                         | No.                                      |
| `connector`  | Defines a re-usable unit of functionality for an integration. `connector` uses a `connection` to interact with external resources.       | No.                                      |
| `schedule`   | Represents a schedule where a periodic task can occur.                                                                                   | No.                                      |
| `service`    | Represents a service that delivers a functionality. Entry point for the process. Service consists of one or more `connector` references. | No.                                      |
