# Secrets CRUD Operations

This lab will cover performing CRUD operations on secrets.

## Before the Lab

Perform the Pre-Lab Setup instructions in `labs/README.md`.

## After the Lab

Perform the Post-Lab Teardown instructions in `labs/README.md`.

## Lab Instructions

### Create a Secret

We want to create a secret named `db` which has a key/value pair of `username=me`.

To create the secret, run:

```
vault kv put secret/db username=me
```

And you should see the following output:

```
Key              Value
---              -----
created_time     2020-04-12T17:06:42.1729398Z
deletion_time    n/a
destroyed        false
version          1
```

### Read the Secret

We want to read the secret we previously created. There are 3 ways to read a secret:

- CLI
- REST API
- GUI

To read the secret in the CLI, run:

```
vault kv get secret/db
```

And you should see the following output:

```
====== Metadata ======
Key              Value
---              -----
created_time     2020-04-12T17:20:49.546425Z
deletion_time    n/a
destroyed        false
version          1

====== Data ======
Key         Value
---         -----
username    me
```

To read the secret from the REST API, run:

```
curl \
  -H "X-Vault-Token: $VAULT_TOKEN" \
  -X GET $VAULT_ADDR/v1/secret/data/db | jq
```

And you should see the following output:

```
{
  "request_id": "c3fdda8f-6373-d11b-6a0c-dd801cf05295",
  "lease_id": "",
  "renewable": false,
  "lease_duration": 0,
  "data": {
    "data": {
      "username": "me"
    },
    "metadata": {
      "created_time": "2020-04-12T17:20:49.546425Z",
      "deletion_time": "",
      "destroyed": false,
      "version": 1
    }
  },
  "wrap_info": null,
  "warnings": null,
  "auth": null
}
```

To read the secret from the GUI:

1. Browse to http://127.0.0.1:8200.
2. Login with the `Root Token`.
3. Navigate to "secret/db".
4. Click the eye icon to reveal the value for `username`.

### Update the Secret

We want to update the secret we previously created.

To update the secret, run:

```
vault kv patch secret/db password=hunter2
```

And you should see the following output:

```
Key              Value
---              -----
created_time     2020-04-12T17:22:05.3840091Z
deletion_time    n/a
destroyed        false
version          2
```

Notice that version is now `2`. That's because Vault versions secrets! Vault's

To read the latest version of our secret, run:

```
vault kv get secret/db
```

To read the first version of our secret, run:

```
vault kv get -version=1 secret/db
```

### Delete the Secret

To delete the secret, run:

```
vault kv delete secret/db
```

And you should see the following output:

```
Success! Data deleted (if it existed) at: secret/db
```

Let's make sure the secret was actually deleted. Run:

```
vault kv get secret/db
```

And you should see the following output:

```
====== Metadata ======
Key              Value
---              -----
created_time     2020-04-12T17:22:05.3840091Z
deletion_time    2020-04-12T17:24:49.7318736Z
destroyed        false
version          2
```

What?! Why's the secret still there?! Deleting a secret simply marks it as deleted, which allows it to be undeleted if desired.

If you try to get the secret from the REST API, you'll get an empty data field. Run:

```
curl -H "X-Vault-Token: $VAULT_TOKEN" -X GET $VAULT_ADDR/v1/secret/data/db | jq
```

And you should see the following output:

```
{
  "request_id": "482bf7c6-d83d-4e36-0fbd-34abcfc012d5",
  "lease_id": "",
  "renewable": false,
  "lease_duration": 0,
  "data": {
    "data": null,
    "metadata": {
      "created_time": "2020-04-12T17:22:05.3840091Z",
      "deletion_time": "2020-04-12T17:24:49.7318736Z",
      "destroyed": false,
      "version": 3
    }
  },
  "wrap_info": null,
  "warnings": null,
  "auth": null
}
```

If you want to permanently delete a secret from Vault, run:

```
vault kv metadata delete secret/db
```

## Summary

- Can create arbitrary key/value secrets at arbitrary paths.
- Secrets are versioned.
- Secret deletion probably doesn't have the behavior you'd expect.
