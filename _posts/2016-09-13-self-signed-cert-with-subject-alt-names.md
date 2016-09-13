---
title: Generating Self-Signed Certificates with Subject Alternative Names
layout: post
category: posts
tags: [openssl, certificates, EC2, Amazon Linux]
---

Recently, for reasons, I had to generate a self-signed certificate with subject alternative names. As an additional wrinkle, I was trying to do it as part of the userdata script for a machine instance in EC2 running Amazon Linux. This turned out to be more difficult than expected.

After piecing together various blog posts this is what I ended up with.

````sh
LOCAL_HOSTNAME=$(GET http://169.254.169.254/latest/meta-data/local-hostname)
LOCAL_IPV4=$(GET http://169.254.169.254/latest/meta-data/local-ipv4)
openssl req \
    -newkey rsa:2048 \
    -nodes \
    -sha256 \
    -x509 \
    -days 3650 \
    -config <(cat <<EOF
[req]
distinguished_name = req_distinguished_name
x509_extensions = v3_req
prompt = no
[req_distinguished_name]
C = US
ST = California
L = Los Angeles
O = Example.com
CN = $LOCAL_HOSTNAME
[v3_req]
keyUsage = nonRepudiation, digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names
[alt_names]
DNS.1 = $LOCAL_HOSTNAME
DNS.2 = $(hostname)
IP.1 = $LOCAL_IPV4
EOF
    ) \
    -keyout server.key \
    -out server.crt
````

The above (almost) single line command:

1. Fetches the private IP and hostname of the EC2 instance.
2. Generates an new RSA private key with 2048 bits: ``-newkey rsa:2048``
3. Doesn't require a password for the private key: ``-nodes``
3. Use the SHA-2 signature algorithm (gotta keep your self-signed certs up to date with the cool kids): ``-sha256``
4. Sets the expiry to 10 years: ``-days 3650``
5. Passes in a custom openssl config: ``-config ...``
6. Writes the private key to a file: ``-keyout server.key``
7. Writes the public key to a file: ``-keyout server.crt``

The custom config file

1. Sets the distinguished name for the certificate
2. Enables x509 extensions (needed for subjectAltName)
3. Specfies that the key is for server authentication
4. Sets the alternative names to the FQDN, the hostname, and the IP address of the instance.

Generating a self-signed certificate this way at least means we can do host verification.
