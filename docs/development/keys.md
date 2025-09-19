# Generate keys with openssl
To generate the PEM keys, you can use the following commands with OpenSSL:

!!! warning "Before you start"
    Make sure to keep your private key secure and do not share it with anyone. The public key can be shared freely.

Before running this, ensure you have OpenSSL installed on your machine. You can check by running `openssl version` in your terminal. If it's not installed, you can download it from [here](https://openssl-library.org/source/). In addition, make sure your terminal is in the directory where you want to store the keys in the commands below.

```bash
  openssl genpkey -algorithm RSA -out "./private_key.pem" -pkeyopt rsa_keygen_bits:2048
  openssl rsa -pubout -in "./private_key.pem" -out "./public_key.pem"
```

After generating the keys, you can continue to [setup the development environment](setup.md).
