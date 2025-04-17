### Generate Ed25519 key pair 
#### Generate private key
```bash
openssl genpkey -algorithm ED25519 -out private.pem
```

#### Extract public key from private key
```bash
openssl pkey -in private.pem -pubout -out public.pem
```

#### View and format the keys for .env file
```bash
# For the private key:
echo "\nTOKEN_PRIVATE_PEM=\"$(awk 'NF {sub(/\r/, ""); printf "%s\\\\n", $0}' private.pem | sed 's/...$//')\"\n"

# For the public key:
echo "\nTOKEN_PUBLIC_PEM=\"$(awk 'NF {sub(/\r/, ""); printf "%s\\\\n", $0}' public.pem | sed 's/...$//')\"\n"
```