ansible-role-rancher-secrets-bridge
=========

Ansible Role to setup [Rancher Secrets Bridge](https://github.com/rancher/secrets-bridge/ "Rancher Secrets Bridge").
The Secrets Bridge is a Rancher service which integrates [Hashicorp Vault](https://www.vaultproject.io/ "Hashicorp Vault") in Rancher so that Docker containers at startup are securely delivered with their secrets within Vault.

The Role provides three features, which is defined by the variable `tasks_to_run` and its list items:
###  - approle
Sets up the AppRole with all policies this Role needs in order to Setup Secrets Bridge configuration on Vault.
###  - config
Sets up the actual Secrets Bridge configuration on Vault. [Configure Vault](https://github.com/rancher/secrets-bridge/blob/master/docs/setup.md#configure-vault "Configure Vault")
###  - rancher
Sets up the [Secrets Bridge Server](https://github.com/rancher/secrets-bridge/blob/master/docs/setup.md#configure-secrets-bridge-server "Secrets Bridge Server") and [Agents](https://github.com/rancher/secrets-bridge/blob/master/docs/setup.md#configure-secrets-bridge-agents "Secrets Bridge Agents") on Rancher.

<img width="917" alt="rancher-secrets-bridge" src="https://user-images.githubusercontent.com/19170248/30802614-0b0b7ec4-a1df-11e7-9b75-628d45821794.png">

Role Variables
--------------

Vault path for storing Secrets Bridge Server configurations

    secrets_brige_config_path: "secret/secrets-bridge"


You can either provide the Rancher environments configuration in ansible vars, in a  git repo or local directory with yml files

    secrets_bridge_config_source:
      type: "ansible" # ['ansible' , 'git' or 'local']
      addr: "" # Provide url/path, required only for 'git' or 'local'

Secrets bridge config. Define either `secrets_bridge_config` in Ansible vars, or write its list content in yaml format into `git` or `loccal` files (depending on `secrets_bridge_config_source`).
```yaml
secrets_bridge_config:
  - name: "Environment1"
    options:
      - scope: "Stack1/service1"
        policy:
          name: "app1"
          options:
            - path: "secret/Environment/Stack1/service1"
              capabilities: '[\"read\", \"list\"]'
      - scope: "Stack2/service2/Stack2-app2-1"
        policy:
          name: "app2"
          options:
            - path: "secret/Environment1/Stack2/service2"
              capabilities: '[\"read\", \"list\"]'
  - name: "Environment2"
    options:
      - scope: "Stack1/app1"
        policy:
          name: "app1"
          options:
            - path: "secret/Environment2/Stack3/service3"
              capabilities: '[\"read\", \"list\"]'

```

Set any custom IDs for the Secrets Bridge AppRole

    secrets_bridge_role_id: ""
    secrets_bridge_secret_id: ""

Rancher Environment API details. Environment API keys can be created in the UI, see [Rancher environment API keys](http://rancher.com/docs/rancher/v1.6/en/api/v2-beta/api-keys/#environment-api-keys "Rancher environment API keys").

```yaml
secrets_bridge_rancher_environment_api:
  - name: "Environment1"
    url: "http://rancher.server.io:8080/v1/projects/123abc"
    access_key: "access_key"
    secret_key: "secret_key"
  - name: "Environment2"
    url: "http://rancher.server.io:8080/v1/projects/456efg"
    access_key: "access_key"
    secret_key: "secret_key"
```

Address to Vault Server

    secrets_bridge_vault_addr: "https//yourvault.server:port"

Example Playbook
----------------
    - hosts: localhost
      vars_prompt:
        - name: "vault_root_token"
          prompt: "Enter Vault token with administration policies"
      roles:
        - role: ansible-role-rancher-secrets-bridge
          tasks_to_run:
            - approle

    - hosts: localhost
      vars_prompt:
      roles:
        - role: ansible-role-rancher-secrets-bridge
          tasks_to_run:
            - setup
            - rancher

License
-------

BSD

Author Information
------------------
Hamza Tumturk
