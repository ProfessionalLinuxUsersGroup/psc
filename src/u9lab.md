<div class="flex-container">
        <img src="https://github.com/ProfessionalLinuxUsersGroup/img/blob/main/Assets/Logos/ProLUG_Round_Transparent_LOGO.png?raw=true" width="64" height="64"></img>
    <p>
        <h1>Unit 9 Lab – Certificates and Keys</h1>
    </p>
</div>

> If you are unable to finish the lab in the ProLUG lab environment we ask you `reboot`
> the machine from the command line so that other students will have the intended environment.

### Required Materials

Putty or other connection tool
Lab Server
Root or sudo command access

STIG Viewer 2.18 (download from <https://public.cyber.mil/stigs/downloads/> )

#### Downloads

The worksheet has been provided below. The document(s) can be transposed to
the desired format so long as the content is preserved. For example, the `.txt`
could be transposed to a `.md` file.

- <a href="./assets/downloads/u9/u9_lab.txt" target="_blank" download>📥 u9_worksheet(`.txt`)</a>
- <a href="./assets/downloads/u9/u9_lab.pdf" target="_blank" download>📥 u9_worksheet(`.pdf`)</a>

### Setting up Rsyslog with TLS

1. Complete the lab: <https://killercoda.com/het-tanis/course/Linux-Labs/211-setting-up-rsyslog-with-tls>

### Review Solving the Bottom Turtle

1. Review pages 41-48 of <https://spiffe.io/pdf/Solving-the-bottom-turtle-SPIFFE-SPIRE-Book.pdf>
    - Does the diagram on page 44 make sense to you for what you did with a certificate authority in this lab?

### SSH – Public and Private key pairs

Complete the lab: <https://killercoda.com/het-tanis/course/Linux-Labs/212-public-private-keys-with-ssh>

1. Complete the lab here: <https://killercoda.com/het-tanis/course/Linux-Labs/110-fail2ban-with-metric-alerting>

   - What is the significance of they permission settings that you saw on the generated
public and private key pairs?

## Digging Deeper challenge (not required for finishing lab)

1. Complete the following labs and see if they reinforce any of your understanding of certificates with
the use of Kubernetes.

    - <https://killercoda.com/killer-shell-cks/scenario/certificate-signing-requests-sign-manually>

    - <https://killercoda.com/killer-shell-cks/scenario/certificate-signing-requests-sign-k8s>

2. Read the rest of <https://spiffe.io/pdf/Solving-the-bottom-turtle-SPIFFE-SPIRE-Book.pdf>
    
    - How does that align with your understanding of zero-trust? if you haven't read about zero-trust, start here:

    - <https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-207.pdf>
