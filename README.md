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
- The Firebird add-on available to Gradle

The add-on is not published to a public repository yet, so build and install it locally first:

```bash
git clone https://github.com/makssent/jmix-firebird-addon
cd jmix-firebird-addon
./gradlew publishToMavenLocal
```

This is why `mavenLocal()` is listed in `repositories` in `build.gradle`. Once the add-on is
published, that line can be removed.

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

## What is Firebird-specific here

Three things differ from a stock Jmix project. They are already applied — this list is here so the
differences are not mistaken for accidents.

**The add-on starter** in `build.gradle`, plus `jmix.data.dbms-type=firebird` and the Jaybird driver.
Without the starter the application fails at startup: Jmix finds no `DbmsFeatures` implementation for
the `firebird` type.

**The add-on's changelog is included first** in `src/main/resources/com/company/firebird/liquibase/changelog.xml`:

```xml
<include file="/io/github/makssent/jmix/firebird/liquibase/changelog.xml"/>
```

It declares Firebird values for the `uuid.type`, `byte_array.type`, `offsetDateTime.type` and
`authserver.data.type` Liquibase properties. Jmix modules reference these but define values only for
the databases they support officially. Liquibase properties are global from the point they are
declared, so the order of includes matters: put this one before any Jmix module changelog, otherwise
`CREATE TABLE ... (ID ${UUID.TYPE} ...)` reaches the database verbatim.

**`firebird` is added to the inserts** in `liquibase/changelog/010-init-user.xml`:

```xml
<insert tableName="USER_" dbms="postgresql, mssql, hsqldb, h2, firebird">
```

The template lists target databases explicitly there, and Firebird is in neither list. Without this
the application starts and creates every table, but `USER_` stays empty and no password works — with
no error in the log. The add-on cannot fix this: `dbms` is an attribute of an individual changeset,
and this file belongs to the application, not to a Jmix jar.

## Learn more about Jmix

* [Documentation](https://docs.jmix.io)
* [Online demo applications](https://www.jmix.io/live-demo)
