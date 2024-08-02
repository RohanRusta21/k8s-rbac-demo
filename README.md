# k8s-rbac-demo

```bash
openssl genrsa -out tom.key 2048
```

- openssl: This is the command-line tool for using the OpenSSL library.
- genrsa: This command generates an RSA private key.
- -out tom.key: Specifies the file where the generated private key will be saved. In this case, it will be saved to a file named tom.key.
- 2048: This specifies the size (in bits) of the RSA key to be generated. A 2048-bit key is commonly used and provides good security.


```bash
openssl req -new -key tom.key -out tom.csr -subj "/CN=tom"
```

- openssl: The command-line tool for using the OpenSSL library.
- req: This command is used for creating and processing certificate requests.
- -new: This option indicates that a new CSR is being created.
- -key tom.key: Specifies the private key to be used to generate the CSR. In this case, it uses the tom.key file created in the previous step.
- -out tom.csr: Specifies the file where the generated CSR will be saved. In this case, it will be saved to a file named tom.csr.
- -subj "/CN=tom": This specifies the subject of the CSR directly on the command line:

    - /CN=tom: CN stands for Common Name, which typically represents the domain name or the name of the entity requesting the certificate. Here, it's set to tom.



```bash
cat tom.csr | base64 | tr -d '\n'
```

- cat tom.csr: This part reads and outputs the contents of the tom.csr file.
- |: This is a pipe, used to pass the output of one command as input to the next command.
- base64: This command encodes the input (in this case, the contents of tom.csr) into base64 format.
- |: Another pipe to pass the output to the next command.
- tr -d '\n': This command removes all newline characters from the input.


```bash
kubectl apply -f csr.yml
kubectl certificate approve tom
```

- kubectl: The Kubernetes command-line tool used to interact with the Kubernetes cluster.
- certificate: A Kubernetes resource type for managing certificate requests.
- approve: This subcommand is used to approve a pending certificate signing request (CSR).
- tom: The name of the CSR that you want to approve.

```bash
kubectl get csr tom -o jsonpath='{.status.certificate}' | base64 --decode > tom.crt
```

- kubectl get csr tom -o jsonpath='{.status.certificate}':

    kubectl get csr tom: Fetches the CSR named tom.
    -o jsonpath='{.status.certificate}': Outputs the status of the CSR in a JSONPath format, specifically extracting the certificate field from the status.

- |: This pipe operator passes the output of the previous command to the next command.

- base64 --decode:

    base64: Command-line tool to encode/decode data in base64 format.
    --decode: Decodes the base64-encoded certificate.

- > tom.crt:

    >: Redirects the output to a file.
    tom.crt: The name of the file where the decoded certificate will be saved.
    
# Setup Kubeconfig for new user

```bash
kubectl config set-credentials tom --client-certificate=tom.crt --client-key=tom.key
kubectl config get-contexts
kubectl config set-context tom-context --cluster=kubernetes --namespace=default --user=tom
kubectl config use-context tom-context
```

### Checking Permissions for User tom

```bash
kubectl auth can-i get pods --as=tom --namespace=default
kubectl auth can-i watch pods --as=tom --namespace=default
kubectl auth can-i list pods --as=tom --namespace=default
```

### Checking Permissions for Service Account tom-sa

```bash
SECRET_NAME=$(kubectl get sa tom-sa --namespace=default -o jsonpath='{.secrets[0].name}')
TOKEN=$(kubectl get secret $SECRET_NAME --namespace=default -o jsonpath='{.data.token}' | base64 --decode)
```

```bash
kubectl auth can-i create pods --token=$TOKEN --namespace=default
kubectl auth can-i delete pods --token=$TOKEN --namespace=default
```
