# Authentication

This lab will cover username-based authentication and policies.

## Before the Lab

Perform the Pre-Lab Setup instructions in `labs/README.md`.

## After the Lab

Perform the Post-Lab Teardown instructions in `labs/README.md`.

## Lab Instructions

### Enable Username Authentication

First, we want to unset the `VAULT_TOKEN` env var. Vault will automatically use `VAULT_TOKEN` if it's set, and in this lab we'll be logging in as different users. We'll still need the `Root Token` at some point, so let's store it as a different variable. Run:

```
export ROOT_TOKEN=$VAULT_TOKEN && unset VAULT_TOKEN
```

Next, explicitly login with the `Root Token`. Run:

```
vault login token=$ROOT_TOKEN
```

Let's see which auth methods are currently enabled. Run:

```
vault auth list
```

And you should see the following output:

```
Path      Type     Accessor               Description
----      ----     --------               -----------
token/    token    auth_token_0ebf4196    token based credentials
```

Notice that `token` is the only enabled auth type. That means that any interaction with Vault requires a token.

To enable the `userpass` auth type, run:

```
vault auth enable userpass
```

Let's create 2 policies: one that only allows reading of secrets and one that allows reading and writing. Run:

```
vault policy write readonly /labs/lab-3/readonly.hcl

vault policy write readwrite /labs/lab-3/readwrite.hcl
```

Then create some users. Run:

```
vault write auth/userpass/users/user-nothing password=pass

vault write auth/userpass/users/user-readonly password=pass policies=readonly

vault write auth/userpass/users/user-readwrite password=pass policies=readwrite
```

Login as `user-nothing`. Run:

```
vault login -method=userpass username=user-nothing password=pass
```

This user has no explicit permissions, so creating a secret will give a 403 response. Run:

```
vault kv put secret/db username=me
```

Next, login as `user-readwrite`. Run:

```
vault login -method=userpass username=user-readwrite password=pass
```

This user has read/write permissions, so they'll be able to create a secret. Run:

```
vault kv put secret/db username=me
```

Finally, login as `user-readonly`. Run:

```
vault login -method=userpass username=user-readonly password=pass
```

This user has readonly permissions, so creating a secret will give a 403 response. Run:

```
vault kv put secret/db username=me
```

But they can read secrets. Run:

```
vault kv get secret/db
```

Login to the GUI as the created user.
