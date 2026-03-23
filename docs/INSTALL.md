# KafkaKombat v1.3 Installation Guide

This document describes environment preparation, execution of the KafkaKombat installer, and basic verification of the installed system.

KafkaKombat is intended for operation **only in Kerberos-based environments**. Before starting the installation, Kerberos must be treated as a mandatory base dependency of the system, not as an optional feature. If the target environment does not use Kerberos as the required access model for Kafka, this distribution is not suitable for installation.

---

## 1. Document Scope

This document is intended for administrators and engineers performing an initial KafkaKombat installation, an upgrade of an existing installation, or deployment of a test environment.

This guide covers:

- preliminary environment preparation
- PostgreSQL preparation
- Kerberos principal and keytab preparation
- installer execution
- application startup
- basic post-install verification

---

## 2. What the Installer Does

The KafkaKombat installer is intended to prepare the application installation and performs the following tasks:

- places application files in the target directory
- prepares runtime configuration
- applies SQL schema and DB patches in PostgreSQL
- prepares the working directory structure of the distribution

---

## 3. What the Installer Does Not Do

The KafkaKombat installer does not perform the following actions:

- does not create the OS user
- does not create the PostgreSQL user
- does not create the PostgreSQL database
- does not create the Kerberos principal
- does not create the keytab
- does not configure an external reverse proxy, TLS termination, or load balancing
- does not register the service through init/systemd unless this is explicitly included in the distribution

These entities and dependencies must be prepared in advance.

---

## 4. Environment Requirements

### 4.1. Operating System

The supported target environment is Linux x86_64.

### 4.2. Java

Java 11 is required.

Use of an organization-approved and supported JDK/JRE build is recommended.

### 4.3. PostgreSQL

PostgreSQL 14 or later is required.

The KafkaKombat host must have network connectivity to the PostgreSQL instance.

### 4.4. Kerberos

The host must have a correctly configured Kerberos client environment, including:

- `krb5.conf`
- KDC availability
- correct hostname and service name resolution
- a prepared KafkaKombat principal and keytab

---

## 5. Preliminary Preparation

### 5.1. OS User

Before installation, create the system user under which KafkaKombat will run.

Example:

```bash
useradd -r -m -d /opt/kafkakombat kafkakombat
```

The actual user name and directory layout are determined by internal operations standards.

### 5.2. Target Directory

It is recommended to define a single installation directory in advance, for example:

```bash
/opt/kafkakombat
```

The distribution package will be unpacked into this directory and the runtime files of the application will be placed there.

---

## 6. PostgreSQL Preparation

### 6.1. Create the User and Database

Before running the installer, create the PostgreSQL user and database.

Example:

```sql
CREATE USER kafkakombat WITH PASSWORD 'strong_password';
CREATE DATABASE kafkakombat OWNER kafkakombat;
```

### 6.2. Verify Connectivity

Verify in advance that the KafkaKombat host can connect to PostgreSQL using the required hostname, port, database, user, and password.

---

## 7. Kerberos Preparation

### 7.1. Principal and Keytab

Before running the installer, prepare:

- a Kerberos principal for KafkaKombat
- the corresponding keytab file

The procedure for creating the principal and keytab depends on the Kerberos infrastructure in use and the internal standards of the organization.

### 7.2. Keytab Placement

It is recommended to place the keytab in the `security/` directory of the distribution or in another pre-approved secure path.

Example:

```bash
security/kafkakombat.keytab
```

### 7.3. File Permissions

The keytab must be accessible only to the user under which KafkaKombat runs.

Example:

```bash
chmod 600 security/kafkakombat.keytab
chown kafkakombat:kafkakombat security/kafkakombat.keytab
```

---

## 8. Distribution Files Preparation

### 8.1. Unpack the Release Package

The KafkaKombat release package must be unpacked into the target installation directory.

### 8.2. Base Layout

After unpacking, the distribution typically contains the `bin/`, `conf/`, `install/`, `logs/`, and `security/` directories.

The exact directory set depends on the release version.

---

## 9. Installation Configuration Preparation

The installation file is typically located at:

```bash
install/install.config
```

It must contain the parameters required for installation, including:

- PostgreSQL connection parameters
- Kerberos principal parameters and keytab path
- file placement paths
- other parameters required by the specific distribution

Before running the installer, verify that all mandatory parameters are correct.

---

## 10. Run the Installation

From the `install/` directory, run the installer provided with the distribution.

Typical example:

```bash
cd install
./install.sh
```

If a specific distribution uses a different startup procedure, use the commands defined for that release.

During installation, the following actions are expected:

- generation of runtime configuration
- preparation of service files
- application of SQL schema and DB patches to PostgreSQL

---

## 11. Start the Application

After installation is complete, start the application using the standard script from the `bin/` directory.

Start the application:

```bash
bin/kafkakombat-service.sh start
```

Stop the application:

```bash
bin/kafkakombat-service.sh stop
```

Restart the application:

```bash
bin/kafkakombat-service.sh restart
```

Check application status:

```bash
bin/kafkakombat-service.sh status
```

If the distribution provides different scripts or startup parameters, follow the documentation for that distribution.

---

## 12. Post-install Verification

After the application is started, it is recommended to verify the following in order:

### 12.1. Process Availability

Verify that the KafkaKombat process is running and does not terminate immediately after startup.

### 12.2. UI Availability

Verify that the web UI opens at the expected address.

### 12.3. Authentication Operation

Verify:

- availability of the login page
- successful authentication
- correct Kerberos integration

### 12.4. PostgreSQL Operation

Verify that the application successfully connected to PostgreSQL and that the SQL schema and DB patches were applied without errors.

### 12.5. Kafka Integration Operation

Verify:

- cluster availability
- topic visibility
- consumer group visibility
- execution of basic user operations

---

## 13. Main Runtime Directories

A typical distribution uses the following directories:

- `bin/` - service scripts for startup and control
- `conf/` - application runtime configuration
- `install/` - installer and installation files
- `logs/` - application logs
- `security/` - keytab and security-related files

---

## 14. Common Issues

### 14.1. No PostgreSQL Connectivity

Check:

- PostgreSQL hostname and port
- database name
- user and password
- network connectivity
- `pg_hba.conf` and access rules

### 14.2. Kerberos Errors

Check:

- correctness of `/etc/krb5.conf`
- principal
- keytab path
- keytab file permissions
- KDC availability

### 14.3. Application Does Not Start

Check:

- application logs in `logs/`
- correctness of the runtime configuration
- correctness of Java 11
- availability of PostgreSQL and Kerberos dependencies

---

## 15. Upgrade

When upgrading KafkaKombat, the following is recommended:
0. the current instance must be stopped, and the rollback order is defined by the internal procedure
1. preserve the current runtime configuration
2. preserve the keytab and security-related files
3. ensure that a PostgreSQL backup is available
4. use the installer that corresponds to the new release version
5. perform post-install verification after the upgrade

The actual upgrade procedure may depend on the delivery model and internal operations procedures.
