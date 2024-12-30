# Dependency Management

## Topic 1 - The Scale of Dependencies

Modern applications rely heavily on external dependencies, which far outweigh the amount of code written by developers. For instance, a single `import numpy` command in Python triggers the execution of thousands of lines of code within the dependency. This highlights that developers are responsible for only a small fraction of an application’s codebase, while dependencies account for the vast majority, underscoring the critical role of dependency management in software security and stability.

Dependency management ensures that external libraries are tracked, maintained, and integrated into the Software Development Lifecycle (SDLC) and DevOps pipelines. Key benefits include:

- Improved application support through awareness of dependencies.
- Version control to enhance stability and compatibility.
- Pre-configured golden images for faster deployments.
- Simplified setup for new developers.
- Security monitoring to address vulnerabilities in dependencies.

Organizations often utilize tools like JFrog Artifactory to centralize dependency management, integrate it into pipelines, and automate dependency provisioning during builds and deployments. This approach enhances both efficiency and security across the development process.

---

## Topic 2 - Internal vs External

Dependencies in software development can be categorized based on their origin into external and internal dependencies. Each type plays a distinct role and comes with unique security considerations.

### External Dependencies

These are developed and maintained outside of the organization, often publicly available (free or paid). Examples include:

- Python libraries from PyPI (e.g., pip packages).
- JavaScript libraries from NPM (e.g., VueJS, NodeJS).
- Paid SDKs like Google’s ReCaptcha.

Since external dependencies are managed by third parties, organizations rely on their maintainers for updates and security patches.

### Internal Dependencies

These are created and maintained within the organization, typically for internal use. Examples include:

- Authentication libraries to standardize internal application logins.
- Data connection libraries for accessing various data sources.
- Message translation libraries for internal communication formats.

Internal dependencies help standardize processes and reduce redundant development efforts, but the organization bears full responsibility for their maintenance.

### Comparison

The origin of a dependency doesn’t inherently determine its quality or security. However, each type has a unique attack surface, requiring tailored security measures. External dependencies demand vigilance against vulnerabilities introduced by third parties, while internal dependencies require robust in-house security practices. Addressing these measures is essential to minimize risks and maintain secure development pipelines.

---

## Topic 3 - Securing External Dependencies

External dependencies introduce unique security challenges due to limited direct control. To maintain application security, it's crucial to manage these dependencies effectively. Key concerns include:

### Public CVEs

Vulnerabilities in dependencies, disclosed as CVEs, create risks as attackers can exploit outdated versions.  
Updating dependencies promptly is essential but may cause stability issues due to version locking or nested dependencies (e.g., Log4j vulnerability).

### Supply Chain Attacks

Attackers may target less-secure dependencies instead of well-hardened applications.  
Example: The MageCart group compromised payment portals and AWS S3 buckets, affecting numerous applications through shared dependencies.  
A common vulnerability is insecure hosting, such as misconfigured S3 buckets with world-writable permissions.

### Defensive Measures

- **Regular Updates**: Patch dependencies promptly, prioritizing critical vulnerabilities.
- **Internal Hosting**: Mirror dependencies internally to limit exposure.
- **Sub-resource Integrity (SRI)**: Use SRI to ensure scripts match expected hashes, preventing tampered libraries from loading.

By proactively managing these risks, organizations can mitigate vulnerabilities and reduce exposure to supply chain attacks.

---

## Topic 4 - Securing Internal Dependencies

### Purpose and Benefits

Internal dependencies refer to libraries or SDKs developed within the organization to streamline processes like authentication, registration, and communication. Their primary goal is to prevent redundant development efforts and promote standardization across applications.

### Key Security Concerns

#### Vulnerability Propagation

A flaw in an internal dependency can affect multiple applications, necessitating rigorous security testing before release.

#### Legacy Code

Dependencies risk becoming outdated if the original developer leaves or updates cease.  
Poor or outdated documentation exacerbates the issue, making it harder for new maintainers to take over effectively.

#### Access and Modification Control

To protect against potential attacks, write-access must be restricted to authorized personnel.  
In some cases, even read access is limited to reduce the risk of exploitation.

### Tools for Managing Internal Dependencies

#### Single-Language Development

- Host internal repositories (e.g., PyPi servers for Python projects).  
- Provides an efficient way to distribute and install dependencies.

#### Multi-Language Development

- Use advanced dependency managers like JFrog Artifactory.  
- Centralizes internal and external dependencies.  
- Supports seamless integration into DevOps pipelines.  
- Enhances control and traceability of dependencies across languages and projects.

### Risks with Dependency Management Tools

While tools like JFrog Artifactory simplify dependency management, they pose significant risks if compromised. One critical threat is Dependency Confusion, which will be elaborated on in the next task.

---

## Topic 5 - Theory of a Dependency Confusion

Dependency Confusion is a supply chain vulnerability where an attacker exploits a race condition between internal and external dependencies managed via dependency managers. Discovered by Alex Birsan in 2021, this vulnerability can force systems to install a malicious dependency in place of an intended internal one.

### Background and Mechanism

#### Normal Behavior

When installing a dependency (e.g., `pip install numpy`), pip fetches the package from the external PyPi repository, selects the latest version, and installs it.

#### Supply Chain Attacks on External Dependencies

- **Typosquatting**: Hosting a package with a similar name (e.g., `nunpy`) to catch developer typos.  
- **Source Injection**: Malicious code introduced via a pull request.  
- **Domain Expiry**: Attackers exploit expired maintainer domains to gain control of the package.

#### Internal Dependencies in Build Steps

Internal dependencies are often included using commands like:

`pip install numpy --extra-index-url https://our-internal-pypi-server.com/`

This flag allows pip to check both internal and external repositories for the package. Pip installs the version with the highest version number.

---

## Topic 6 - Practical Exploitation of Dependency Confusion

Terminate the Task 4 machine and start the assigned machine for this task. If your AttackBox is inactive, redeploy it. Simulate an external PyPI repository using the following command to edit your `/etc/hosts` file:

`sudo bash -c "echo 'MACHINE_IP external.pypi-server.loc' >> /etc/hosts"`

### Environment Overview

The machine simulates a build environment featuring:

- **Internal Dependency Manager**: PyPI  
- **Build Server**: Docker-Compose  

The environment rebuilds an API application every 5 minutes. Verify functionality with:

`curl http://MACHINE_IP:8181/api/list`

### Defense Strategies

- Actively maintain internal dependencies.  
- Secure hosting infrastructure:  
  - Use `--index-url` to enforce a single private feed.  
  - Control dependency scopes.  
  - Utilize client-side verification (e.g., version locking).  
  - Register internal dependency names on external repositories to prevent misuse.

---

## Topic 7 - Conclusion

In this room, we discussed the security controls and misconfigurations commonly found with dependency management. This is by no means an exhaustive list of what should be considered for the security of dependencies. However, to summarise, we should be considering the following:

- Be aware of the dependencies you use in your applications and systems. Also, be aware that these dependencies may have dependencies, which will grow the list of dependencies you will need to keep tabs on.  
- Make sure to always use the latest versions of dependencies, both internal and external dependencies. More often than not, these updates to dependencies are not to introduce new features but to fix existing issues and bugs.  
- It is not just the dependencies themselves that should be considered for security, but also how we configure and use our dependency managers, especially for internal dependencies.  
- Dependencies and dependency management systems should be included in the attack surface of the application or system we are developing.