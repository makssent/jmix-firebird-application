# Jmix Firebird Application

A [Jmix](https://www.jmix.io) application configured to run on **Firebird**, generated from the
standard Jmix application template and adapted for Firebird with the
[jmix-firebird-addon](https://github.com/makssent/jmix-firebird-addon).

Use it as a starting point instead of switching a fresh Jmix project from its default database by
hand.

Built against Jmix 3.0.0.

## Prerequisites

- JDK 17 or newer
- A running Firebird server (3.0 or newer)

## Setup

Create an empty database with UTF-8 as the default character set:

```sql
CREATE DATABASE 'localhost:/path/to/your/database.fdb' DEFAULT CHARACTER SET UTF8;
```

Then fill in the connection settings in `src/main/resources/application.properties`:

```properties
main.datasource.url=jdbc:firebird://localhost:3050//path/to/your/database.fdb?encoding=UTF8
main.datasource.username=YOUR_DB_USER
main.datasource.password=YOUR_DB_PASSWORD
```

## Running

```bash
./gradlew bootRun
```

The application will be available at <http://localhost:8080>. Liquibase creates the schema on the
first start.

Default credentials:

* Login: `admin`
* Password: `admin`

**WARNING**: change the admin password and remove the `ui.login.defaultUsername` and
`ui.login.defaultPassword` properties before deploying anywhere real.

## Firebird documentation

See the [Jmix Firebird documentation](https://github.com/makssent/jmix-firebird-docs) for details on
integrating Firebird into a Jmix application.

## Learn more about Jmix

* [Documentation](https://docs.jmix.io)
* [Online demo applications](https://www.jmix.io/live-demo)
