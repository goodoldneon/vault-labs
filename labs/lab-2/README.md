# Dynamic Secrets

## Initial Setup

1. Run `npm start`
2. Run `docker logs vault_vault_1`, and save `Unseal Key` and `Root Token`.
3. Run `docker exec -it vault_vault_1 bash`
4. Run `export VAULT_TOKEN=<root_token>`
   - Where `<root_token>` is the `Root Token.
5. Run `vault login token=$VAULT_TOKEN`

### Lab Instructions

1. AWS credentials are generated on-demand.
   - Mention `least_duration`.
2. Manully revoke the generated AWS credentials.
