---

title: "Resource Identifier Scheme for OETP"
author:
 -
    ins: N. Lukianets
    name: Nikita Lukainets
    organization: Open Ethics Initiative
    email: n.lukianets@openethics.ai
created: 2022-07-31
tags: #OpenEthics #DITAI #disclosure #identity #protocol #OETP #schema

---

# Resource Identifier Scheme for OETP
This repository contains documentation for the Resource Identifier (RI) Scheme: `oetp`.

[![DOI](https://zenodo.org/badge/519716697.svg)](https://zenodo.org/badge/latestdoi/519716697)

## Abstract
"Big Tech" is an important driver of innovation, however, the consequent concentration of power creates Big Risks for the economy, ethical use of technology, and basic human rights (we consider privacy as one of them).

A decentralization of SBOM (Software Bill of Materials) and data processing disclosures was earlier described as a key requirement for the Open Ethics Transparency Protocol, [OETP](https://openethics.ai/oetp/).

Fulfillment of this requirement allows disclosure formation and validation by multiple parties and avoids harmful concentration of power. To allow efficient decentralization and access to the disclosures of autonomous systems, such as AI systems powered by trained machine learning models, the vendor (or a developer) MUST send requests to a Disclosure Identity Provider, which in turn, processes structured data of the disclosure with a cryptographic signature generator, and then stores the integrity hash with the persistent storage, for example using  Federated Identity Provider. This process was described in the Open Ethics Transparency Protocol I-D document, however, the exact way how to access disclosures was not described there. The specification for the RI scheme described here closes this gap.

## Requirements for disclosure RI
- Provide an identifier referring to each unique disclosure
- Distinguish disclosures created via different disclosure providers
- Ensure that the disclosure schema is easily accessible and understandable by the end-users
- Ensure that the disclosure schema is easily implementable in the software development process
- Ensure that the schema is future-proof for the web3 applications

## Definitions
- **Disclosure**: Disclosure (Ethics Disclosure, or self-disclosure) is application-specific information about the data collection, data processing, and decision-making practices of a Product, provided by the Product Vendor (an individual developer or an organization).
- **Vendor**: A legal person (an individual developer or an organization) that owns one or several end-user Products, or acts as a Supplier and provides Components for other Vendors.
- **Product**: An IT system in the form of software, software as a service system, application, software component, application programming interface, or a physically embodied automated decision-making agent.
- **Component**: An IT system supplied by Vendor and integrated/embedded into end-user Products. Components themselves do not necessarily interface with end-users.
- **Disclosure Identity Provider**: The automated Disclosure processing is enabled by requests to both the Open Ethics Disclosure database powered by Disclosure Identity Providers (DIP) and the Product's OETP Disclosure file, stored in the product's website root following OETP specification. DIP serves as a service point to generate and retrieve generated disclosures.

## Implementation
Disclosures may be resolved in URLs. LSID was the inspiration for the first version of the schema.

Syntax:

    https://disclosure.openethics.ai/urn:oetp:alias:oe:component:68ff9aad-fa28-45fe-ac9b-61823385ccc0

In the example above the URL contains urn-style path for the disclosure of the `component:68ff9aad-fa28-45fe-ac9b-61823385ccc0` via generator alias `alias:oe`.

The `lsid` namespace, however, is not registered with the Internet Assigned Numbers Authority (IANA), and so these are not strictly URNs or URIs.

### Variables
- `<alias>`: stores the alphanumerical alias which is unique for each Disclosure Identity Provider. Example: `oe`
- `<hash>`: stores the SHA3-512 hash generated for the disclosure object. The Disclosure is organized into a predefined data schema and MUST be cryptographically signed by the Signature Generator (Open Ethics or federated providers) using standard SHA3-512 hash implementation. The integrity hash MUST be appended to disclosure as the OETP.schema.integrity element. Example: `156d624b8f2dbea87128a2147f255842652475c5dc595c79f64c90c7ff486d59007c3e18c993e3163395812e26b70ea70dfc413f7ca128869d115f12e5699bf2`
- `<disclosure>`: stores the Disclosure ID, unique to the namespace of the DIP alias. Example: `954c3f63-73ac-4261-8b77-a40cf0ea0f13`
- `<component>`: stores the unique Component ID issued during the first disclosure. Products also get Component IDs when they go through the disclosure process for the first time. Example: `68ff9aad-fa28-45fe-ac9b-61823385ccc0`
- `<domain>`: The product's web domain, should be validated using DV.

### Use-cases

<img src="diagrams/OETP - Resource Identifier (RI) Scheme name_ oetp - diagram.png" alt="A diagram with examples of URI schemes for disclosures of the products' components performed via Disclosure Identity Providers">

To satisfy the mentioned requirements for disclosure RI, it is proposed to use the following formats instead of URLs:

#### Use-case 1: accessing disclosure via its integrity hash
Syntax:

    oetp://<hash>

Here integrity `<hash>` is the SHA3-512 generated during the disclosure process

#### Use-case 2: accessing disclosure using component ID and generator's alias
Syntax:

    oetp://<component>@<alias>[:<disclosure>]

Here `<component>` is the ID assigned via Disclosure Identity Provider under its `<alias>` during the first disclosure.

#### Use-case 3: accessing disclosure product's domain
Syntax:

    oetp://<domain>[:<disclosure>]

For verified domains (DV), disclosure could be accessed using product's `<domain>` instead of `<component>@<alias>`.)

### Domain Validation
Product domain validation is issued automatically based on issuing criteria and is linked to the Disclosure Identity Provider.

The sole criterion is proof of control over whois records, DNS records file, email, or web hosting account of a domain. Typically control over a domain is determined using one of the following:

* Response to an email sent to the email contact in the domain's whois details
* Response to an email sent to a well-known administrative contact in the domain, e.g. (admin@, postmaster@, etc.)
* Publishing a DNS TXT record
* Publishing a nonce provided by an automated certificate issuing system

**IMPORTANT:** Domain validation does not assure that any particular legal entity is connected to the domain, even if the domain name may imply a particular legal entity controls the domain.

### Identity of the Disclosure Identity Provider
Disclosure Identity Provider can obtain the alphanumeric alias from Open Ethics by sending a request to [contact@openethics.ai](mailto:contact@openethics.ai), specifying desired alias and a registered PEN number. Example: `oe, 1.3.6.1.4.1.59149`

If you are not sure whether your organization has registered a PEN, please check the [IANA PEN registry](https://www.iana.org/assignments/enterprise-numbers/enterprise-numbers). If your organization does have a PEN, we recommend obtaining a sub-assignment from the contact listed in the registry.

## Examples
accessing disclosure directly via its known integrity `<hash>`

    oetp://156d624b8f2dbea87128a2147f255842652475c5dc595c79f64c90c7ff486d59007c3e18c993e3163395812e26b70ea70dfc413f7ca128869d115f12e5699bf2

`<disclosure>`=”954c3f63-73ac-4261-8b77-a40cf0ea0f13” for the product with `<domain>`=”limejam.com” 

    oetp://limejam.com:954c3f63-73ac-4261-8b77-a40cf0ea0f13
    
last disclosure for the `<component>`=”68ff9aad-fa28-45fe-ac9b-61823385ccc0”, generated via Disclosure Identity Provider with `<alias>`=”oe”

    oetp://68ff9aad-fa28-45fe-ac9b-61823385ccc0@oe:last

## Application
Open Ethics is currently implementing the scheme to be used with the Open Ethics Transparency Protocol [OETP](https://openethics.ai/oetp/) to access disclosures for software products and their components, as well as to access disclosures for [public surveillance locations](https://openethics.ai/public-surveillance-transparency/) to improve transparency and prevent unlimited surveillance with unlawful access to private data.

## Contribution
The URI scheme specification has been developed to satisfy the decentralization requirements of the OETP. The simple scheme syntax allows easy access to the AI ethics disclosures formed using the Open Ethics Transparency Protocol.

The URI scheme description is accessible in [oetp-ri-scheme.txt](oetp-ri-scheme.txt). The scheme was submitted for publication in IANA [list of URI schemes](https://www.iana.org/assignments/uri-schemes/uri-schemes.xhtml) with `provisional` status.


## Acknowledgments
This work was supported by the H2020 Programme of the European Commission under Article 15 of Grant Agreement No. 951972 StandICT.eu 2023 under the project "DITAI - Disclosure Identity for the Transparency of Artificial Intelligence Systems".

## Authors
Nikita Lukianets - n.lukianets@openethics.ai
