# Usage

This page documents useful flags and options supported by Ratify

## Environment variables

- `RATIFY_LOG_LEVEL`: configure the log level. Valid options are
  - `PANIC`
  - `FATAL`
  - `ERROR`
  - `WARNING`
  - `INFO` (default)
  - `DEBUG`
  - `TRACE`
- `RATIFY_CONFIG`: change the default Ratify configuration directory. Defaults to `~/.ratify`

## Feature flags

Ratify may roll out new features behind feature flags, which are activated by setting the corresponding environment variable `RATIFY_<FEATURE_NAME>=1`.
A value of `1` indicates the feature is active; any other value disables the flag.

- `RATIFY_DYNAMIC_PLUGINS`: (disabled) Enables Ratify to download plugins at runtime from an OCI registry by setting `source` on the plugin config

- `RATIFY_CERT_ROTATION`: (disabled) Enables Ratify to rotate TLS certificates automatically when they are about to expire. See [cert-controller](https://github.com/open-policy-agent/cert-controller) for more details on the implementation. The cert-controller checks the validation of certificates every 12 hours, if the certificate is expiring in 90 days, cert-controller will generate a new certificate that is valid for 10 years. Notes: as this [post](https://ahmet.im/blog/kubernetes-secret-volumes-delay/) pointed out, it may take Kubernetes 60-90 seconds to progagate changes to Secrets on the mounted volumes. If you provided invalid/expired certificates/keys during the service startup, it may take up to 90 seconds for the service to rotate the certificates and get to actual working state with mounted certs.

Notes: the root CA certificate generated by cert-controller will have the Subject field like:

  `Subject: O = Ratify, CN = ratify.gatekeeper-system`
  
  and x509v3 extentions field like:
  ```
  X509v3 extensions:
    X509v3 Subject Alternative Name: 
      DNS:ratify.gatekeeper-system
    X509v3 Basic Constraints: critical
      CA:TRUE
    X509v3 Key Usage: critical
      Digital Signature, Key Encipherment, Certificate Sign
  ```
So if you want to generate your own root CA certificate, make sure it has the same Subject and x509v3 extensions fields.
