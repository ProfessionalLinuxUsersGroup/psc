<div class="flex-container">
        <img src="https://github.com/ProfessionalLinuxUsersGroup/img/blob/main/Assets/Logos/ProLUG_Round_Transparent_LOGO.png?raw=true" width="64" height="64"></img>
    <p>
        <h1>Unit 9 - Certificate and key madness</h1>
    </p>
</div>

## Overview

In today’s interconnected world, the integrity and security of transmitted data are paramount. As systems grow in complexity and interdependence, it’s crucial to verify the identity of those we communicate with and to protect the data in transit. Certificates and keys form the backbone of this trust. By securely exchanging and validating cryptographic keys and digital certificates, we establish a system where data can be encrypted, identities can be authenticated, and communications can be trusted.

## Learning Objectives

1. Defining
2. Explore change management frameworks, including CMDBs and baselines.
3. Implement detection tools like AIDE to monitor file system integrity.
4. Use Ansible to remediate drift and enforce configuration state.
5. Connect drift management to compliance, auditability, and incident response.

## Prerequisites

1. Basic command line navigation and editing skills (`vi`, `cat`, `grep`)
2. Experience with editing config files using Vim
3. Familiarity with key generation (Learned in prior chapters)

## Relevance & Context

Certificates and Keys ensure trust and authenticity in both human and machine interactions. Whether securing APIs, internal services, or user sessions over HTTPS, public key infrastructure (PKI) allows systems to validate each other’s identities and encrypt traffic accordingly. These concepts are foundational in implementing secure DevOps pipelines, enforcing compliance standards like HIPAA or PCI-DSS, and ensuring resilience in infrastructure. Understanding how keys are generated, used, and validated is a critical skill for system administrators, security engineers, and DevOps professionals alike.

## Key Terms & Definitions

- TLS
- Symmetric Keys
- Asymmetric Keys
- Non-Repudiation
- Anti-Replay
- Plaintext
- Cypher-Text
- Fingerprints
- Passphrase (in key generation)