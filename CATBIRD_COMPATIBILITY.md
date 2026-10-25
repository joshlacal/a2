# Catbird dependency compatibility fork

This fork updates the APNs client used by Catbird's Rust services. Its upstream
base is walletconnect/a2 commit `b0eb7d5c7f033ec0b4d340b29990a6ef9b21fa40`
(`v0.10.0`). It is consumed by immutable Git revision; this work does not publish
a new package or change Apple's API endpoints.

The transport uses Rustls 0.23.46 and hyper-rustls 0.27.10. Both client-auth modes
explicitly select the ring TLS provider, so they work when a consumer also
enables AWS-LC through another dependency. WebPKI roots, HTTPS-only connections,
HTTP/2, client certificates, request fields, and token-signing choices are
preserved. The deprecated rustls-pemfile dependency is replaced by the maintained
PEM decoder re-exported by rustls-pki-types. Certificate-chain iteration and the
existing PKCS8-only private-key contract are retained; this does not add PKCS1 or
SEC1 private-key support.

Supporting dependencies are updated to the stable releases observed on
2026-10-25, and the minimum Rust version is 1.95 because parking_lot 0.13 requires
it. The dependency-refresh integration uses Rust 1.99.1.

Validation includes the upstream request/payload/signature tests under both the
default OpenSSL token signer and the optional ring signer. New local TLS tests
exercise a trusted root and hostname successfully, reject an unknown root, and
reject a wrong hostname while both TLS providers are enabled. PEM tests preserve
the existing key-format boundary and accept a multi-certificate chain. These
tests use local generated or upstream fixture certificates; they do not send
APNs notifications or establish production delivery.

The upstream crate enables `clippy::unwrap_used` as a warning in its existing
tests. Library Clippy is checked with warnings denied; all-target Clippy retains
those test-only warnings without introducing a lint exemption.
