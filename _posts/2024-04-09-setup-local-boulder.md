---
layout: post
title: "Setting up a local Boulder to test integration with ACME"
date: 2024-04-09 17:55:00 +0800
---

Certificate Authorities (CAs) like Let's Encrypt, ZeroSSL, and Google Trust
Services offer [ACME](https://datatracker.ietf.org/doc/html/rfc8555) support.
Of particular interest is Let's Encrypt; they open source their CA software,
[Boulder](https://github.com/letsencrypt/boulder), on GitHub.

Let's Encrypt has a staging API for testing purposes. During development, it's
easy to mess around with the immature code. By having a local version of
Boulder, we could experiment freely without affecting the CA servers.

We could use the ACME staging API for the staging environment and the local
Boulder for deployment.

Another advantage of having our own Boulder is that we can set the rate limit
to ridiculously low to better stimulate when the extreme situation happens.

## Boulder Setup

The [Boulder repository](https://github.com/letsencrypt/boulder?tab=readme-ov-file#setting-up-boulder)
is well documented on how to set it up with Docker.

### 1: Clone the repository

```bash
git clone https://github.com/letsencrypt/boulder/
cd boulder
```

### 2: Tweak Configurations

If you're on a Mac, the Docker server runs inside a VM and has its isolated
network. We need to expose some ports, take DNS challenge as an example. Before
issuing the certificate, we need to go through a challenge to prove the
ownership of the domain name, the ACME server sends back a token, we need to do
some calculation, the result should be put into a challenge TXT record.

While we're in a development environment, you definitely don't want to provision
the real DNS records on your domain name. Behind the scenes, Boulder has a Pebble
challenge test server to help us easily mock the challenge result.

The test server with HTTP management API listens on port **8055**.

1. Open __docker-compose.yml__.
2. Append "8055:8055" to the field __services:boulder:ports__.

```yaml
# ...
services:
  boulder:
    # ...
    ports:
      - 4001:4001 # ACMEv2
      - 4002:4002 # OCSP
      - 4003:4003 # OCSP
      - 8055:8055 # Pebble Challenge Test Server <- Add this line
    # ...
# ...
```

If you want to customize the rate limits as well, edit the file
__tests/rate-limit-policies.yml__ to adjust thresholds and windows.

### 3: Launch Boulder

```bash
docker compose up
```

Wait for a few moments, after everything is up and running, the endpoint should
be at __http://localhost:4001/directory__.

## Mocking

Create a Pebble implementation to handle the record manipulation while
provisioning challenge resources. In the DNS challenge, all we need to do is
mock the TXT record response.

Check out the [Pebble documentation](https://github.com/letsencrypt/pebble/blob/629e80b8edcd46b7788a58e2752890e68a0cae23/cmd/pebble-challtestsrv/README.md)
to explore additional mocking abilities.

### Add TXT Record

To mock a "DNS-01" challenge response for "_acme-challenge.example.com" with
the value "foo":

```bash
curl -d '{"host":"_acme-challenge.example.com.", "value": "foo"}' http://localhost:8055/set-txt
```

### Remove TXT Record

To remove the mocking of the "DNS-01" challenge response for
"_acme-challenge.example.com":

```bash
curl -d '{"host":"_acme-challenge.example.com."}' http://localhost:8055/clear-txt
```

Note that the host name must be in complete domain name (FQDN) format with a
trailing dot.
