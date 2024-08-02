# k8s-rbac-demo

```bash
openssl genrsa -out tom.key 2048
```
```
openssl: This is the command-line tool for using the OpenSSL library.
genrsa: This command generates an RSA private key.
-out tom.key: Specifies the file where the generated private key will be saved. In this case, it will be saved to a file named tom.key.
2048: This specifies the size (in bits) of the RSA key to be generated. A 2048-bit key is commonly used and provides good security.
```

```bash
openssl req -new -key tom.key -out tom.csr -subj "/CN=tom"
```

```
openssl: The command-line tool for using the OpenSSL library.
req: This command is used for creating and processing certificate requests.
-new: This option indicates that a new CSR is being created.
-key tom.key: Specifies the private key to be used to generate the CSR. In this case, it uses the tom.key file created in the previous step.
-out tom.csr: Specifies the file where the generated CSR will be saved. In this case, it will be saved to a file named tom.csr.
-subj "/CN=tom": This specifies the subject of the CSR directly on the command line:

    /CN=tom: CN stands for Common Name, which typically represents the domain name or the name of the entity requesting the certificate. Here, it's set to tom.
    /O=group1: O stands for Organization, representing the organization to which the entity belongs. Here, it's set to group1.
```
