# Dynamic Credentials

In this lab, we'll cover dynamic credential generation. Creating on-demand credentials allows you to scope credentials to individual servers and users. If a server has a security breach, and its credentials are stolen, you can revoke those credentials without affecting any other server.

Vault automatically deletes dynamic credentials after a configurable length of time. Having short-lived credentials helps to mitigate the fallout of unknown security breaches. If credentials have a lifespan of 1 hour, then nefarious parties have a short window before they're locked out.

This lab will demonstrate dynamic MySQL credentials, but Vault has [a wide array of integrations](https://www.vaultproject.io/docs/secrets).

## Before the Lab

Perform the Pre-Lab Setup instructions in `labs/README.md`.

## After the Lab

Perform the Post-Lab Teardown instructions in `labs/README.md`.

## Lab Instructions

### Give Vault the Ability to Create MySQL Users

You should currently be shelled into the Vault container.

Enable the database secrets engine:

```
vault secrets enable database
```

See if the `database` secret engine is enabled:

```
vault secrets list
```

Configure Vault with the proper plugin and connection information:

```
vault write database/config/my-mysql-database \
    plugin_name=mysql-database-plugin \
    connection_url="{{username}}:{{password}}@tcp(mysql:3306)/" \
    allowed_roles="my-role" \
    username="root" \
    password="foo"
```

Vault doesn't need `root` user credentials, but its user must have privileges to create other users, namely the `GRANT OPTION` privilege. For the sake of workshop simplicity, we'll give Vault the `root` user.

Specify how to map a name in Vault to a SQL statement that creates the user:

```
vault write database/roles/my-role \
    db_name=my-mysql-database \
    creation_statements="CREATE USER '{{name}}'@'%' IDENTIFIED BY '{{password}}';GRANT SELECT ON *.* TO '{{name}}'@'%';" \
    default_ttl="1h" \
    max_ttl="24h"
```

### Create Dynamic MySQL Credentials

Ask for MySQL credentials:

```
vault read database/creds/my-role
```

You should see output like this:

```
Key                Value
---                -----
lease_id           mysql/creds/readonly/a5YOD4AaYJTAaGYhg9yVstmn
lease_duration     1h
lease_renewable    true
password           bbd1984c-2810-0231-29a9-c2bdd7e865b0
username           read-root-8450a4
```

Vault has created a new user on-demand. We could use this feature to easily scoped DB credentials to individual processes. If we discover a server breach, then we can revoke its credentials without affecting any other server.

Additionally, the `lease_duration` property is used to give a lifetime to credentials. If we set `lease_duration` to `1h`, then credentials will only be valid for an hour. This mitigates potential credential theft by shortening the time window they can be used.

### Use the Dynamic Credentials

Open another terminal, and then shell into the MySQL container:

```
docker exec -it vault_mysql_1 bash
```

Open MySQL's CLI using the dynamic credentials. Run the following command (replace `<username>` and `<password>` with the values from the dynamic credentials):

```
mysql -u<username> -p<password>
```

You now be in the MySQL CLI. Quit the MySQL CLI be running `exit`.

### Revoke the Dynamic Credentials

Let's pretend that our server was breached. The DB credentials may be compromised, so we want to revoke access immediately.

Back in your Vault terminal, revoke the dynamic credentials. Run the following command (replace `<lease_id>` with the value from the dynamic credentials):

```
vault lease revoke <lease_id>
```

In your MySQL terminal, try logging in again. You should see an `Access denied` error.

## Summary

- Create on-demand credentials.
- Can revoke credentials at any time.
- Dynamic credentials have a configurable lifespan.
- Can create dynamic credentials for a wide array of services.
