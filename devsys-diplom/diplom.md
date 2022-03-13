# Курсовая работа по итогам модуля "DevOps и системное администрирование"

1. Создайте виртуальную машину Linux.
\+
2. Установите ufw и разрешите к этой машине сессии на порты 22 и 443, при этом трафик на интерфейсе localhost (lo) должен ходить свободно на все порты.\
![installufw](img/installufw.JPG)\
![allowportsufw](img/allowufw.JPG)
3. Установите hashicorp vault ([инструкция по ссылке](https://learn.hashicorp.com/tutorials/vault/getting-started-install?in=vault/getting-started#install-vault)). \
![vault](img/installvault.JPG) \
![verifyvault](img/verifyinstallvault.JPG)
4. Cоздайте центр сертификации по инструкции ([ссылка](https://learn.hashicorp.com/tutorials/vault/pki-engine?in=vault/secrets-management)) и выпустите сертификат для использования его в настройке веб-сервера nginx (срок жизни сертификата - месяц). \
```bash
vagrant@vagrant:~$ vault server -dev -dev-root-token-id=root
==> Vault server configuration:

             Api Address: http://127.0.0.1:8200
                     Cgo: disabled
         Cluster Address: https://127.0.0.1:8201
              Go Version: go1.17.5
              Listener 1: tcp (addr: "127.0.0.1:8200", cluster address: "127.0.0.1:8201", max_request_duration: "1m30s", max_request_size: "33554432", tls: "disabled")
               Log Level: info
                   Mlock: supported: true, enabled: false
           Recovery Mode: false
                 Storage: inmem
                 Version: Vault v1.9.3
             Version Sha: 7dbdd57243a0d8d9d9e07cd01eb657369f8e1b8a

==> Vault server started! Log data will stream in below:

2022-01-30T20:25:29.843Z [INFO]  proxy environment: http_proxy="\"\"" https_proxy="\"\"" no_proxy="\"\""
2022-01-30T20:25:29.843Z [WARN]  no `api_addr` value specified in config or in VAULT_API_ADDR; falling back to detection if possible, but this value should be manually set
2022-01-30T20:25:29.844Z [INFO]  core: Initializing VersionTimestamps for core
2022-01-30T20:25:29.844Z [INFO]  core: security barrier not initialized
2022-01-30T20:25:29.844Z [INFO]  core: security barrier initialized: stored=1 shares=1 threshold=12022-01-30T20:25:29.846Z [INFO]  core: post-unseal setup starting
2022-01-30T20:25:29.848Z [INFO]  core: loaded wrapping token key
2022-01-30T20:25:29.848Z [INFO]  core: Recorded vault version: vault version=1.9.3 upgrade time="2022-01-30 20:25:29.848772632 +0000 UTC m=+0.060444730"
2022-01-30T20:25:29.848Z [INFO]  core: successfully setup plugin catalog: plugin-directory="\"\""
2022-01-30T20:25:29.848Z [INFO]  core: no mounts; adding default mount table
2022-01-30T20:25:29.850Z [INFO]  core: successfully mounted backend: type=cubbyhole path=cubbyhole/
2022-01-30T20:25:29.851Z [INFO]  core: successfully mounted backend: type=system path=sys/
2022-01-30T20:25:29.851Z [INFO]  core: successfully mounted backend: type=identity path=identity/
2022-01-30T20:25:29.856Z [INFO]  core: successfully enabled credential backend: type=token path=token/
2022-01-30T20:25:29.857Z [INFO]  rollback: starting rollback manager
2022-01-30T20:25:29.857Z [INFO]  core: restoring leases
2022-01-30T20:25:29.858Z [INFO]  expiration: lease restore complete
2022-01-30T20:25:29.858Z [INFO]  identity: entities restored
2022-01-30T20:25:29.858Z [INFO]  identity: groups restored
2022-01-30T20:25:29.858Z [INFO]  core: post-unseal setup complete
2022-01-30T20:25:29.859Z [INFO]  core: root token generated
2022-01-30T20:25:29.859Z [INFO]  core: pre-seal teardown starting
2022-01-30T20:25:29.859Z [INFO]  rollback: stopping rollback manager
2022-01-30T20:25:29.859Z [INFO]  core: pre-seal teardown complete
2022-01-30T20:25:29.859Z [INFO]  core.cluster-listener.tcp: starting listener: listener_address=127.0.0.1:8201
2022-01-30T20:25:29.859Z [INFO]  core.cluster-listener: serving cluster requests: cluster_listen_address=127.0.0.1:8201
2022-01-30T20:25:29.859Z [INFO]  core: post-unseal setup starting
2022-01-30T20:25:29.859Z [INFO]  core: loaded wrapping token key
2022-01-30T20:25:29.859Z [INFO]  core: successfully setup plugin catalog: plugin-directory="\"\""
2022-01-30T20:25:29.860Z [INFO]  core: successfully mounted backend: type=system path=sys/
2022-01-30T20:25:29.861Z [INFO]  core: successfully mounted backend: type=identity path=identity/
2022-01-30T20:25:29.861Z [INFO]  core: successfully mounted backend: type=cubbyhole path=cubbyhole/
2022-01-30T20:25:29.862Z [INFO]  core: successfully enabled credential backend: type=token path=token/
2022-01-30T20:25:29.862Z [INFO]  rollback: starting rollback manager
2022-01-30T20:25:29.862Z [INFO]  core: restoring leases
2022-01-30T20:25:29.862Z [INFO]  identity: entities restored
2022-01-30T20:25:29.862Z [INFO]  identity: groups restored
2022-01-30T20:25:29.862Z [INFO]  expiration: lease restore complete
2022-01-30T20:25:29.862Z [INFO]  core: post-unseal setup complete
2022-01-30T20:25:29.862Z [INFO]  core: vault is unsealed
2022-01-30T20:25:29.864Z [INFO]  expiration: revoked lease: lease_id=auth/token/root/h366bd743eb8a6cb7d8881460cad038b981ec639bb6aa7d4da7d801d928e93daf
2022-01-30T20:25:29.866Z [INFO]  core: successful mount: namespace="\"\"" path=secret/ type=kv
2022-01-30T20:25:29.876Z [INFO]  secrets.kv.kv_7347f9ad: collecting keys to upgrade
2022-01-30T20:25:29.876Z [INFO]  secrets.kv.kv_7347f9ad: done collecting keys: num_keys=1
2022-01-30T20:25:29.876Z [INFO]  secrets.kv.kv_7347f9ad: upgrading keys finished
WARNING! dev mode is enabled! In this mode, Vault runs entirely in-memory
and starts unsealed with a single unseal key. The root token is already
authenticated to the CLI, so you can immediately begin using Vault.

You may need to set the following environment variable:

    $ export VAULT_ADDR='http://127.0.0.1:8200'

The unseal key and root token are displayed below in case you want to
seal/unseal the Vault or re-authenticate.

Unseal Key: Jw0oiND+GaOqQMqYtQutWppLPCQ4PowZQQy8CaVuCmU=
Root Token: root

Development mode should NOT be used in production installations!
```

```bash
vagrant@vagrant:~$ vault policy write admin admin-policy.hcl
Success! Uploaded policy: admin
vagrant@vagrant:~$ vault policy list
admin
default
root
vagrant@vagrant:~$ vault policy read admin
# Read system health check
path "sys/health"
{
  capabilities = ["read", "sudo"]
}

# Create and manage ACL policies broadly across Vault

# List existing policies
path "sys/policies/acl"
{
  capabilities = ["list"]
}

# Create and manage ACL policies
path "sys/policies/acl/*"
{
  capabilities = ["create", "read", "update", "delete", "list", "sudo"]
}

# Enable and manage authentication methods broadly across Vault

# Manage auth methods broadly across Vault
path "auth/*"
{
  capabilities = ["create", "read", "update", "delete", "list", "sudo"]
}

# Create, update, and delete auth methods
path "sys/auth/*"
{
  capabilities = ["create", "update", "delete", "sudo"]
}

# List auth methods
path "sys/auth"
{
  capabilities = ["read"]
}

# Enable and manage the key/value secrets engine at  path

# List, create, update, and delete key/value secrets
path "secret/*"
{
  capabilities = ["create", "read", "update", "delete", "list", "sudo"]
}

# Enable secrets engine
path "sys/mounts/*" {
  capabilities = [ "create", "read", "update", "delete", "list" ]
}

# List enabled secrets engine
path "sys/mounts" {
  capabilities = [ "read", "list" ]
}

# Work with pki secrets engine
path "pki*" {
  capabilities = [ "create", "read", "update", "delete", "list", "sudo" ]
}
```
роль и сертификат:
```bash
vagrant@vagrant:~$ ADMIN_TOKEN=$(vault token create -format=json -policy="admin" | jq -r ".auth.client_token")
vagrant@vagrant:~$ echo $ADMIN_TOKEN
s.3I0pzw9XHm6LTbF7n1o6v9vR

consul-template



vault write -field=certificate pki/root/generate/internal \
     common_name="devsys-pdc-2.com" \
     ttl=730h > CA_cert.crt
	 
vault write -format=json pki/root/sign-intermediate csr=@pki_intermediate.csr \
     format=pem_bundle ttl="730h" \
     | jq -r '.data.certificate' > intermediate.cert.pem

vault write pki_int/roles/devsys-pdc-2-dot-com \
     allowed_domains="devsys-pdc-2.com" \
     allow_bare_domains=true \
     allow_subdomains=true \
     max_ttl="730h"


vault write pki_int/issue/devsys-pdc-2-dot-com common_name="diplom.devsys-pdc-2.com" ttl="720h"

```
сам сертификат для поддомена:
```bash
vagrant@vagrant:~$ vault write pki_int/issue/devsys-pdc-2-dot-com common_name="diplom.devsys-pdc-2.com" ttl="720h" > my.crt
Key                 Value
---                 -----
ca_chain            [-----BEGIN CERTIFICATE-----
MIIDsDCCApigAwIBAgIUBgdkkmkp1Pi+a7BGzX9JOKwX5NMwDQYJKoZIhvcNAQEL
BQAwGzEZMBcGA1UEAxMQZGV2c3lzLXBkYy0yLmNvbTAeFw0yMjAxMzAyMTIzMzla
Fw0yMjAzMDIwNzI0MDlaMDIxMDAuBgNVBAMTJ2RldnN5cy1wZGMtMi5jb20gSW50
ZXJtZWRpYXRlIEF1dGhvcml0eTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoC
ggEBAMo7AOGJ4a+mlROhTVjdN/t7Qovkb52NPhWSZ63vMhbD0MOtE1Z/AGXBBFe9
kN+hg6KV9V15RI5iqcsl1MNOrJANaU2xd8pZE+LB8NXE5/bkkHpDe3EJW7a+UYhV
iS4grRUXkDADEHda69c0lUMi1hj51JnkbwEl8tBcuhdm5yG2ZLXU+MON+Jg+Umrx
yQ/kQciAvAf7kGPjBbBjgAWO+w48ZPXOJWtom2fvZL/ZkONXk/yU46m8oTuf+/WY
TEaKbI6Dt57zmLWqcuPBuDufxN0ksZ1+P8pNYxL+XcSV1FhgGEtnkM4FKsMMxI+t
XsWjk1FLpvgec8QtiqPjrb53KCMCAwEAAaOB1DCB0TAOBgNVHQ8BAf8EBAMCAQYw
DwYDVR0TAQH/BAUwAwEB/zAdBgNVHQ4EFgQU79pd6YA+D8AyeUN3QjIDECRi4pow
HwYDVR0jBBgwFoAU/nErj1NKF0bRClC9umArUY5C+iMwOwYIKwYBBQUHAQEELzAt
MCsGCCsGAQUFBzAChh9odHRwOi8vMTI3LjAuMC4xOjgyMDAvdjEvcGtpL2NhMDEG
A1UdHwQqMCgwJqAkoCKGIGh0dHA6Ly8xMjcuMC4wLjE6ODIwMC92MS9wa2kvY3Js
MA0GCSqGSIb3DQEBCwUAA4IBAQAsCj0ELBfKaLtAoGv/67t/T6f82esejm6NX/yb
K8i2/J5P7X/QwvjH2KZQlQyUEAGj7Y4aHGoDZZqdkWx0EZSeh1DGCBosch7M4qUw
DObaCZqUr1mJwRuPmp4N/1DLtUgv188SdU51d/C13LsZcFWpghypGY3SY4ZzIfZO
vtcREkuDIyOsOQZQ/dDu4zQ8qgz+ILawW+nKzVLhxzCpc+Qknoz1SjZ2QKE1aQ5C
fN16vil//ilzcKvE1yHwOChAoQ/lTN9stTM1iqF844OyH7tyUbGlhr/np3bJRRaQ
q7+XC5/uZsll15qjVWw91Trk/YduiHE59xMruai/o9YfPZ8r
-----END CERTIFICATE-----]
certificate         -----BEGIN CERTIFICATE-----
MIIDeTCCAmGgAwIBAgIUXyAvMIe/FzohI+3EqGSUH5vl1qcwDQYJKoZIhvcNAQEL
BQAwMjEwMC4GA1UEAxMnZGV2c3lzLXBkYy0yLmNvbSBJbnRlcm1lZGlhdGUgQXV0
aG9yaXR5MB4XDTIyMDEzMDIxMjk0M1oXDTIyMDMwMTIxMzAxM1owIjEgMB4GA1UE
AxMXZGlwbG9tLmRldnN5cy1wZGMtMi5jb20wggEiMA0GCSqGSIb3DQEBAQUAA4IB
DwAwggEKAoIBAQDCdH7jS0W/zOwOgBCPbRi4egaKTli4RpwdEi/BVmZgjUAMOzMH
r3DMePeHjILbd05/eEx+m8nupnCBMDP0Rf28VJKPQPHRP7VjOrK+M9Cr/YlrH3R/
3Yq+zjVD11wi77VnG7KIjspODKd88nhqbs/RjtWb75FvnzdVMJ0pXMfx1naAAm5v
ojxLtje5xpxoGpBrNZEG3VtHB2KDN2dcenrZSd2plrNE729FWCwcvaVh3bBfRtTy
Ab6XjRB/0/O13CJxNkJKbxVCKWkclW1WvJz+0It8bCR838giCWK0Ne6YYm8zbv5K
uNuhJ7FxhAxa2BP2q4L4deP9XD8ULazdQUPrAgMBAAGjgZYwgZMwDgYDVR0PAQH/
BAQDAgOoMB0GA1UdJQQWMBQGCCsGAQUFBwMBBggrBgEFBQcDAjAdBgNVHQ4EFgQU
GAKGxGop671vIwCZPQ3uu6MzZXAwHwYDVR0jBBgwFoAU79pd6YA+D8AyeUN3QjID
ECRi4powIgYDVR0RBBswGYIXZGlwbG9tLmRldnN5cy1wZGMtMi5jb20wDQYJKoZI
hvcNAQELBQADggEBAH9GIpTPehwPYc+o0hgjoMokYsU7jI7NjxNswWAJxsOxuu7v
bFoDww1VK32tQxlcD554sPIJW+mu3gSKAdJ5ma1rcqGjZgvNq9JjFsih7IgvaeQ/
melWHeV3fh80K5EtZKOY8VQ/prQSIWDESJXaCcrWDGhAHpzu4oCNmFYCLDgVoCwv
GGIcm6BHS+wOI7cGHVOAa9p6ZBJw8J/SqtS6XxIDD5z5nscyTFI9KUpYz70uMXCw
vdEC8EC/s0jCC0dIhD2J6wCc1fNGole6XYeDMsaF4+XggJrI8GT7M4nGVo1knp2e
fhkEgWwkEigSpOWnEbRS4d9UAvd0ASA8jI7igRw=
-----END CERTIFICATE-----
expiration          1646170213
issuing_ca          -----BEGIN CERTIFICATE-----
MIIDsDCCApigAwIBAgIUBgdkkmkp1Pi+a7BGzX9JOKwX5NMwDQYJKoZIhvcNAQEL
BQAwGzEZMBcGA1UEAxMQZGV2c3lzLXBkYy0yLmNvbTAeFw0yMjAxMzAyMTIzMzla
Fw0yMjAzMDIwNzI0MDlaMDIxMDAuBgNVBAMTJ2RldnN5cy1wZGMtMi5jb20gSW50
ZXJtZWRpYXRlIEF1dGhvcml0eTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoC
ggEBAMo7AOGJ4a+mlROhTVjdN/t7Qovkb52NPhWSZ63vMhbD0MOtE1Z/AGXBBFe9
kN+hg6KV9V15RI5iqcsl1MNOrJANaU2xd8pZE+LB8NXE5/bkkHpDe3EJW7a+UYhV
iS4grRUXkDADEHda69c0lUMi1hj51JnkbwEl8tBcuhdm5yG2ZLXU+MON+Jg+Umrx
yQ/kQciAvAf7kGPjBbBjgAWO+w48ZPXOJWtom2fvZL/ZkONXk/yU46m8oTuf+/WY
TEaKbI6Dt57zmLWqcuPBuDufxN0ksZ1+P8pNYxL+XcSV1FhgGEtnkM4FKsMMxI+t
XsWjk1FLpvgec8QtiqPjrb53KCMCAwEAAaOB1DCB0TAOBgNVHQ8BAf8EBAMCAQYw
DwYDVR0TAQH/BAUwAwEB/zAdBgNVHQ4EFgQU79pd6YA+D8AyeUN3QjIDECRi4pow
HwYDVR0jBBgwFoAU/nErj1NKF0bRClC9umArUY5C+iMwOwYIKwYBBQUHAQEELzAt
MCsGCCsGAQUFBzAChh9odHRwOi8vMTI3LjAuMC4xOjgyMDAvdjEvcGtpL2NhMDEG
A1UdHwQqMCgwJqAkoCKGIGh0dHA6Ly8xMjcuMC4wLjE6ODIwMC92MS9wa2kvY3Js
MA0GCSqGSIb3DQEBCwUAA4IBAQAsCj0ELBfKaLtAoGv/67t/T6f82esejm6NX/yb
K8i2/J5P7X/QwvjH2KZQlQyUEAGj7Y4aHGoDZZqdkWx0EZSeh1DGCBosch7M4qUw
DObaCZqUr1mJwRuPmp4N/1DLtUgv188SdU51d/C13LsZcFWpghypGY3SY4ZzIfZO
vtcREkuDIyOsOQZQ/dDu4zQ8qgz+ILawW+nKzVLhxzCpc+Qknoz1SjZ2QKE1aQ5C
fN16vil//ilzcKvE1yHwOChAoQ/lTN9stTM1iqF844OyH7tyUbGlhr/np3bJRRaQ
q7+XC5/uZsll15qjVWw91Trk/YduiHE59xMruai/o9YfPZ8r
-----END CERTIFICATE-----
private_key         -----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAwnR+40tFv8zsDoAQj20YuHoGik5YuEacHRIvwVZmYI1ADDsz
B69wzHj3h4yC23dOf3hMfpvJ7qZwgTAz9EX9vFSSj0Dx0T+1YzqyvjPQq/2Jax90
f92Kvs41Q9dcIu+1ZxuyiI7KTgynfPJ4am7P0Y7Vm++Rb583VTCdKVzH8dZ2gAJu
b6I8S7Y3ucacaBqQazWRBt1bRwdigzdnXHp62UndqZazRO9vRVgsHL2lYd2wX0bU
8gG+l40Qf9PztdwicTZCSm8VQilpHJVtVryc/tCLfGwkfN/IIglitDXumGJvM27+
SrjboSexcYQMWtgT9quC+HXj/Vw/FC2s3UFD6wIDAQABAoIBAFJeqJU9gGkzs9ac
luc6bltOjVCuyVQdzOlfl80M/Ubu7m9/8CjUPzLOFwRbF99BVYYXfkevVEV8SXHf
6lISB95r8L0v5H8fvoUvCyIYrs78EebHp0C2ppwWm2eGZWgDJFNt3x2LZtTS9Wuv
BiUHlY6OM5vfSjr/NaAnfJtVyvh8gw8kpyhcOmwaxusIFxNo2ChOPWwig4Fl9m6x
H8OAGFNz1WcmZQ9NJC/kPUVxMGlfn+wFWOPndUq0DriqIstev7rdXbq0HWygzyUR
moL3ZiPRvs6k7LUOagmqQFfiGYZhAmOo2vJkaLomH0e91OquePByrL5zspoY9SN5
OS/o7AECgYEAyGr0yWh2tjJ2JRakQKl4A/ky+k/BEPINoNY/J+n3j26kblpEMzqy
e5Ps4cGxzKKBjC5VA2CwmvbJtCkUqcHkDaHFkIOQeL1wBYKuPQ9FhaD8dSkv2mjK
wT/27Z1AE1Fi2oF744qxEj8KGaJCXmz7CeXwqJFbtML8OL1b4CXEPgECgYEA+GI0
H5w29eg3pOJGM530VrW6ysbcjd8cQ+WJ9n+6KulI2gDpwnc8Na6qyebxRX4GV1ZY
RWXARDmInYm0+ZUVVMiuDvqFYd8jt0Ko/jNmXyBzZK1enjdagFbz7LhJo9U1X1U7
1FT5U31UcF7FVGbDi5CJAj97exPLzeYvFpSOWesCgYAzW4Ui34wcIE46LtUkZMkG
a6NulWVqDD0FzCEe/0ubC8VDKgpz/kSiekOnjlUblUERRQYv+WUIMNl7O1O6NJ9W
BvBzmeM5rYG5R8WSjAlKtvg6pbVHiLb8UoHaXgN/b8mfKLMwmefKPzE9ZGERhWfH
H49OAwG3DohHme6jP8pMAQKBgA07FfG1HYxd0VD8KKcpO9G+nnOcwjTgdJ39RSsX
RLByesn6EHmmZlOpuTlGDwYXn3jZhZw0vlgkBG333ucIlPlOM09i8uXyv/FMILph
vItGrD7pqUeeRTje6OFsM6gBfHVWkfhOinVzEKNnuPU1ACeYDGRNQ2Ob+1g9cbav
NSH/AoGAWQHX37yLesiPGx4FziDf40Hs70gHmTb1K4ef9MuhhSF5+AnTug037p2e
XTJyIIcaWVUweKOw4MKI09suPAkUowu0ViY75jFcIaIL0kh9OkVOP3ZX/4g+ks4N
rNJxMMbSaRcimY50SdJEHLg69oFFCjZEMBuRzSvCfmlGP8PiUDo=
-----END RSA PRIVATE KEY-----
private_key_type    rsa
serial_number       5f:20:2f:30:87:bf:17:3a:21:23:ed:c4:a8:64:94:1f:9b:e5:d6:a7



```

5. Установите корневой сертификат созданного центра сертификации в доверенные в хостовой системе. \
сертификат был добавлен через certmgr

6. Установите nginx.
```bash
vagrant@vagrant:~$ sudo apt-get update && sudo apt-get install nginx
vagrant@vagrant:~$ sudo systemctl start nginx
vagrant@vagrant:~$ sudo vim /etc/nginx/nginx.conf
...
http {
...
  server {
      listen              443 ssl;
      server_name         diplom.devsys-pdc-2.com;
      ssl_certificate     /etc/ssl/my.crt;
      ssl_certificate_key /etc/ssl/my.key;
vagrant@vagrant:~$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful      
vagrant@vagrant:~$ sudo systemctl restart nginx
```

7. По инструкции ([ссылка](https://nginx.org/en/docs/http/configuring_https_servers.html)) настройте nginx на https, используя ранее подготовленный сертификат:
  - можно использовать стандартную стартовую страницу nginx для демонстрации работы сервера;
  - можно использовать и другой html файл, сделанный вами; \
8. Откройте в браузере на хосте https адрес страницы, которую обслуживает сервер nginx. \
![trustedcert](img/cert.JPG)

9. Создайте скрипт, который будет генерировать новый сертификат в vault:
  - генерируем новый сертификат так, чтобы не переписывать конфиг nginx;
  - перезапускаем nginx для применения нового сертификата. 
```bash
vagrant@vagrant:~$ sudo vim gen_certificate
vault write -format=json pki_int/issue/devsys-pdc-2-dot-com common_name="diplom.devsys-pdc-2.com" ttl="720h" > /etc/ssl/mygen.crt
vault write -format=json pki_int/issue/example-dot-com common_name="test.example.com" ttl="730h" > /etc/ssl/mygen.crt

cat /etc/ssl/mygen.crt | jq -r .data.certificate > /etc/ssl/mygen.crt.pem
cat /etc/ssl/mygen.crt | jq -r .data.ca_chain[ ] >> /etc/ssl/mygen.crt.pem
cat /etc/ssl/mygen.crt | jq -r .data.private_key > /etc/ssl/mygen.crt.key
vagrant@vagrant:~$ sudo systemctl restart nginx
```
проверка:
```bash
vagrant@vagrant:~$ sudo crontab -e
...
* * * * * /home/vagrant/gen_certificate
vagrant@vagrant:~$ sudo service cron status
● cron.service - Regular background program processing daemon
     Loaded: loaded (/lib/systemd/system/cron.service; enabled; vendor preset: enabled)
     Active: active (running) since Sat 2022-01-30 22:16:01 UTC; 4min 12s ago
       Docs: man:cron(8)
   Main PID: 2865 (cron)
      Tasks: 1 (limit: 1071)
     Memory: 1.1M
     CGroup: /system.slice/cron.service
             └─2865 /usr/sbin/cron -f

Jan 30 22:16:02 vagrant CRON[2916]: pam_unix(cron:session): session closed for user vagrant
Jan 30 22:16:02 vagrant CRON[2915]: pam_unix(cron:session): session closed for user root
Jan 30 22:17:01 vagrant CRON[2937]: pam_unix(cron:session): session opened for user vagrant by (uid=0)
Jan 30 22:17:01 vagrant CRON[2938]: (vagrant) CMD (/home/vagrant/gen_certificate)
Jan 30 22:17:01 vagrant CRON[2937]: pam_unix(cron:session): session closed for user vagrant
Jan 30 22:18:01 vagrant CRON[2960]: pam_unix(cron:session): session opened for user vagrant by (uid=0)
Jan 30 22:18:01 vagrant CRON[2961]: (vagrant) CMD (/home/vagrant/gen_certificate)
```
10. Поместите скрипт в crontab, чтобы сертификат обновлялся какого-то числа каждого месяца в удобное для вас время. \
генерировать сертификат каждое 28е число месяца:
```bash
sudo crontab -e

01 1 28 * * /home/vagrant/gen_certificate
```
