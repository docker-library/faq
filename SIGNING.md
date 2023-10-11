# Docker Official Image Signing based on OpenPubkey and TUF

This is a list of frequently asked questions related to the new image signing approach based on [OpenPubkey](https://github.com/openpubkey/) and [TUF](https://theupdateframework.io/) as announced at [DockerCon 23](https://github.com/openpubkey/)

## Can I sign and verify my own images with Docker's signing solution today?
​
We do not recommend you use any of this in production yet, but we do encourage you to give it a try and give us feedback.


Take a look at the [demo](https://github.com/openpubkey/demo)
​
## How does OpenPubkey compare to Sigstore?
​
OpenPubkey cannot really be compared to Sigstore. Sigstore is an end-to-end artifact signing solution, whereas OpenPubkey only binds public keys to OIDC identities for use as part of a larger signing solution. OpenPubkey can be complementary to Sigstore, for example [https://github.com/sigstore/fulcio/issues/1056](https://github.com/sigstore/fulcio/issues/1056).
​
## Why isn’t Docker using Sigstore for signing Docker Official Images?
​
Sigstore has been adopted by the community to sign any type of artifact, so Docker considered very carefully whether or not to use Sigstore for DOI attestation signatures.
​
However, we have some objectives (see below) that would not be met by the current Sigstore architecture.
​
Docker would like DOI signatures to be verified by default by as many Docker users as possible. To this end, Docker will create a TUF root to distribute Trust Policy (see TUF question below). Given that Docker’s users will necessarily need to trust Docker’s TUF root, an additional trusted party (i.e., a CA) could weaken the overall security of the system.
​
Moreover, in considering image signing in an on-premises scenario, we think that OpenPubkey is preferable where:
​
* OIDC infrastructure is already in place, so no additional components need to be deployed
* Depending on and trusting an external third party is undesirable or forbidden
​
Docker hopes that technologies like TUF, Git, OIDC, Transparency Logs, OpenPubkey and Sigstore will continue to evolve for the benefit of the industry and the community. We believe that OpenPubkey will help us deliver the experience we want, with the ease that our users and customers have come to expect from Docker.
​
## What is OpenPubkey?
​
[OpenPubkey](https://github.com/openpubkey) is a Linux Foundation project that enables binding of ODIC identities to ephemeral signing keys. OpenPubkey is an integral part of the new Docker Official Images attestation signing and verification story, along with GitHub Actions, The Update Framework (TUF), and Transparency Logs.
​
## How does TUF fit in with Docker’s DOI signing solution?
​
Docker is planning on using TUF to distribute Trust Policy and updates to verification code to clients (e.g., `docker` CLI). Trust Policy includes:
​
* Which attestations should be present on DOI (i.e., SBOM and provenance)
* Which identities should have signed the above attestations
* Which artifacts should not be trusted (e.g., because it was compromised upstream)
* The current and historical public GitHub Actions OIDC public keys (as related to OpenPubkey certificates)
​
## What is [The Update Framework](https://theupdateframework.io/) (TUF)?
​
The Update Framework (**TUF**) helps developers maintain the security of software update systems, providing protection even against attackers that compromise the repository or signing keys. TUF provides a flexible framework and [specification](https://theupdateframework.github.io/specification/latest/) that developers can adopt into any software update system.
​
## Is Docker using a Transparency Log?
​
The GitHub Action that builds DOI will log all DOI signatures to a public Transparency Log. This will enable Docker and the Community to monitor the DOI signing process to detect any unauthorized signing activity.
​
Transparency Logs are immutable, tamper-resistant ledgers typically based on Merkle trees.
​
[SigStore’s Rekor](https://github.com/sigstore/rekor) is a good example of how Transparency Logs are used in software supply chain security.
​
Web PKI uses Transparency Logs to monitor SSL certificate issuance (see [Certificate Transparency](https://certificate.transparency.dev/)).
​
## How does OpenPubkey fit in with Docker’s new DOI signing solution?
​
Docker is using OpenPubKey to sign DOI attestations on GitHub Actions, but is not ruling out signing with different or additional technologies in the future. We see TUF as a vehicle to deliver Trust Policy to users, and this mechanism could be used to distribute trust policy for one or more signing technologies such as Notation, Sigstore, or OpenPubkey.
​
## Can I use OpenPubkey to sign my own images on GitHub?
​
We recommend you do not use OpenPubkey to signed images for production use yet.


That said, we plan to update the [_Build and push Docker images_ GitHub Action](https://github.com/marketplace/actions/build-and-push-docker-images) to sign images with OpenPubkey by default. Until then, a small change to your GitHub Actions workflow will be necessary if you want to give it a try. See the [GitHub Actions workflow in the openpubkey/demo repository](https://github.com/openpubkey/demo/blob/b6d10590e5333378d91ff4f15bf63ae0bc0c9d28/.github/workflows/docker-build.yaml#L37-L40) for an example of how to do this.
​
## Can I use OpenPubkey to sign images on-prem/using my own OIDC provider?
​
We recommend you do not use OpenPubkey to sign images for production use yet. However, we hope to get OpenPubkey signing integrated into BuildKit (and thus `docker build`) at some point soon. Until then, there is a fork you can try: [https://github.com/openpubkey/buildkit](https://github.com/openpubkey/buildkit). Currently this is tied to GitHub Actions OIDC provider, but we intend to quickly add support for other OIDC providers, including those that are not necessarily publicly available. In this case, you will also need to maintain an internal log of OIDC “public” keys for signature verification. You may want to consider maintaining your own TUF root for internal policy distribution, in which case, this is a good place to maintain a log of OIDC public keys.
​
## How do I verify image signatures once GitHub Actions has rotated its OIDC signing keys?
​
Docker will maintain a log of historical GitHub Actions OIDC public keys in TUF so that signatures created using OpenPubkey on GitHub Actions can be verified after rotation. Because you will necessarily be trusting Docker's TUF root, you will be trusting Docker to maintain an accurate history of these keys. It is simple for Docker and the communit to monitor this log and detect issues with it.
​
## What if compromised DOIs are signed on Docker’s behalf?
​
The GitHub Action workflow building and signing DOI will be logging all signatures to a Transparency Log. This enables Docker and the community to detect illegitimate signatures. Should specific images be identified as compromised, they can be block-listed in Docker’s TUF root so that using those images will be disallowed.
​
## What if an artifact upstream was compromised?
​
As above, should specific images be identified as compromised or containing malicious code, they can be block-listed in Docker’s TUF root so that using those images will be disallowed.
​
## What does this mean for Notary (Docker Content Trust)?
​
We are currently in the process of getting feedback from the wider community on our approach for signing images with OpenPubkey. Once we are confident that OpenPubkey is a stable approach, we intend to start deprecating the use of Notary and working with users that currently depend on Notary to migrate over to using OpenPubkey if they wish to do so. We will work to make sure this transition is as smooth as possible.
