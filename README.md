# test-mutual-tls
Lil Flask app with mutual TLS

The passphrases used in this example is `password`.

## Generating certs

### Generate private key

`openssl genrsa -des3 -out myCA.key 2048`

### Create root cert

`openssl req -x509 -new -nodes -key myCA.key -sha256 -days 1825 -out myCA.pem`

### Create client key/cert request

`openssl req -newkey rsa:2048 -days 1000 -nodes -keyout client.key > client.csr`

#### Sign cert

`openssl x509 -sha256 -req -in client.csr -out client.crt -CA myCA.pem -CAkey myCA.key -CAcreateserial -days 1095`

`openssl ca -cert myCA.pem -keyfile myCA.key -in client.csr -out client.crt`

### Create server key/cert

`openssl genrsa -aes256 -out server.key 4096`

`openssl rsa -in server.key -out server.unc.key`

`openssl req -new -key server.key -out server.csr`

`openssl x509 -CAcreateserial -req -days 365 -in server.csr -CA myCA.pem -CAkey myCA.key -out server.crt`


## Sample `curl` request

```bash
curl https://localhost:5000/ --cacert myCA.pem --cert client.crt --key client.key
```
