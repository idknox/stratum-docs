---
title: SSL certs
category: ssl
alias: ["stratum/articles/guides/self-service-SSL/", "stratum/articles/ssl-self-signed", "stratum/articles/ssl-verify"]
summary: Uploading and updating SSL certificates on Stratum.
---

The [Stratum CLI](/stratum/articles/cli-stratum) allows complete self-management of both SSL Certificates. The [certs](/paas/paas-cli-reference#certs) command group in the CLI has several subcommands to manage your SSL certificates - the usage of each is described below.

## Obtaining a Certificate

For production usage of Stratum, your certificate must:

1. Be signed by a Certificate Authority.
2. Be unencrypted (no password).
3. In PEM format.

For development/staging purposes, a self-signed certificate can be used. To generate one locally, `openssl` can be used:

```
# Create a key
openssl genrsa -out self.key 2048
# Generate the certificate signing request
openssl req -new -key self.key -out self.csr
# Generate the self-signed certificate (remember not to set a challenge password)
openssl req -x509 -days 365 -key self.key -in self.csr -out self.crt
```

Please note - Catalyze does not generate CSRs for you. You will need to generate this on your own.

For production-ready (CA-Signed) certificates, if you do not already have one, we recommend [Digicert](https://www.digicert.com/).

If you are not sure that your certificate is valid for your intended hostname, you can use the [ssl verify](/paas/paas-cli-reference#ssl-verify) CLI command to check it.

## Upload a Cert

To upload the certificate to Stratum, the [certs create](/paas/paas-cli-reference#certs-create) command is used, taking the form `catalyze certs create <cert name> <path to crt file> <path to key file>`. For example:

```
catalyze certs create example.com example.com.crt example.com.key
```

If that cert is self-signed, pass the `-s` option:

```
catalyze certs create example.com example.com.crt example.com.key -s
```

> ***Note:*** Using a self-signed cert can be very useful for development or staging environments.

For wildcard certs, the typical nomenclature is `*.domain.tld`:

```
catalyze certs create *.example.com wildcard-example.com.crt wildcard-example.com.key
```

Before actually uploading the cert, the command will check several things. First, the certificate and private key are checked to make sure they match cryptographically. Next, the given hostname is checked against the Subject of the certificate. Lastly, the command checks if a chain from your certificate all the way to a root CA can be found. This ensures your certificate and private key will be trusted by web browsers. The last two checks will only pass if your certificate is not self signed. If you are uploading a self signed certificate, use the `-s` flag to tell the CLI to skip the hostname and root CA chain check.

If a chain from your certificate to a root CA cannot be found, the CLI will attempt to resolve this and download intermediate certificates and a root CA. This is enabled by default, and ensures a proper certificates and private keys are uploaded to Stratum. However, you can disable certificate chain resolution by passing `-r false`. If you would like to perform the certificate chain resolution as a distinct task, use the [ssl resolve](/paas/paas-cli-reference#ssl-resolve) command.

Once all checks pass, the certificate and private key are uploaded to Stratum. It is important to note, however, that the cert is not yet in use. For a cert to be used, it must be applied to one or more [sites](/stratum/articles/concepts/sites).

## Update a Cert

To update a certificate (if it's expiring soon, or just needs to be replaced), the [certs update](/paas/paas-cli-reference#certs-update) command is used, uploading a new cert and key to replace the old.

```
catalyze certs update *.example.com new-wildcard-example.com.crt new-wildcard-example.com.key
```

> ***Note:*** Cert updates will not take effect until the [Service Proxy](/stratum/articles/concepts/service-proxy) is [redeployed](/stratum/articles/concepts/services#redeploying).

## Listing your Uploaded Certs

Use the [certs list](/paas/paas-cli-reference#certs-list) command:

```
catalyze certs list
```

### See also

* [Initial Setup](/stratum/articles/initial-setup)
* [Sites](/stratum/articles/concepts/sites)
* [Service Proxy](/stratum/articles/concepts/service-proxy)
