## Useful features:

- Node, Python, Ruby, Go support.
- Helm, Docker, OCI support.
- Proxy public packages. All public libraries/packages should only be sourced from this registry. The registry will proxy requests to external registries as necessary. This allows for, but is not a requirement in the registry itself, control over security scanning, blocks malicious packages and versions and avoids upstream outages.
- Ability to integrate with security scanning tools or built-in security scanning.
- Visibility into package use. Critical packages, heavy dependencies, orphan packages, etc.
- “Mature” (well supported, well documented, good API, good search, highly available).
- Easy to maintain and upgrade.
- Role based access control (RBAC).
- OAuth/SSO/SAML/Okta integration.
- Choice of SaaS or self-hosted.



### Gemfury

> https://gemfury.com/help

- Supported formats: RubyGems, npm, Yarn, Python, PHP Composer, APT/DEB, YUM/RPM, NuGet, Go modules, Maven, Bower, Git.
- Only SaaS.
- CLI, Web UI.
- Collaboration feature, permission management, organization accounts.

##### Pricing

> https://fury.co/pricing

- Team 50P - $100/mo



### Harbor

> https://goharbor.io/

- Supported formats: Docker (OCI), Helm.
- Identity integration and RBAC.
- Vulnerabilities scanning, images signing.
- Proxy cache.
- REST API and Web UI.
- Replication.
- Authentication: internal DB, LDAP/AD, OIDC.
- Access metrics.
- Garbage collection.

##### Pricing

- Free and open source.

##### Verdict

Very good product. It supports only Docker and Helm (all OCI-compliant artifacts) but does it well.



### JFrog Artifactory

> https://www.jfrog.com/confluence/display/JFROG/JFrog+Artifactory

- Supported clients and formats: Alpine, Bower, Cargo, Chef, CocoaPods, Conan, Conda, CRAN, Debian, Docker, Git LFS, Go Registry, Gradle, Helm, Maven, npm, NuGet, Opkg, P2, PHP Composer, Puppet, PyPI, RPM, RubyGems, SBT, Vagrant, VCS.
- Integrated with build tools and CI servers: Jenkins, TeamCity, Bamboo, Azure DevOps, GitHub Actions, Bitbucket Pipelines, Maven Artifactory Plugin, Gradle Artifactory Plugin, Drone.
- Cloud, On-Prem, Hybrid solution.
- HA with active/active clustering and multi-site replication.
- Features: custom metadata, file statistics, monitoring, etc.
- Offers a variety of storage solutions (S3, Cloud Storage, etc.)
- REST API, CLI, Webhooks, AQL, Web UI.
- SSO, SAML, LDAP, OAuth.
- RBAC.

##### Pricing

> https://jfrog.com/pricing/

- Cloud (Pro Team), 20GB Transfer/month, 4GB Storage - $100 month.
- Cloud (Enterprise), 200GB Transfer/month, 125GB Storage - $700 month. Additional features included: open source license checks, SAML SSO, replication, etc.
- Self-Hosted (Pro) - $3,200 year.
- Self-Hosted (Pro X) - $20,000 year. Additional features included: open source license checks, vulnerability scanning, etc.



### Google Artifact Registry

> https://cloud.google.com/artifact-registry/docs/overview

- Docker (OCI), Helm, Java\Maven\Gradle (Preview), Node.js (Preview), Python (Preview, GA this year), Debian (Alpha), RPM (Alpha), APT\Yum (Preview).
- Integration with GitHub, Bitbucket, Cloud Source, Cloud Build.
- Deploy directly to GKE, App Engine, Cloud Functions.
- Repository-native IAM with granular permissions.
- Can detect vulnerabilities in container images (only Linux OSes and Java packages).
- Define deployment policies with Binary Authorization feature.
- Regional and multi-regional repositories.
- Encryption at rest by default, additional CMEK encryption can be configured.
- Integrated with Google Cloud’s tooling and runtimes: Cloud Audit logs, gcloud CLI, REST API, Web UI.

Google's roadmap:

- Proxy support for 3rd party artifacts.
- Scanning of all artifacts types.
- Package usage stats (low priority).
- Ruby gem support (low priority).
- Golang support.

##### Pricing

> https://cloud.google.com/artifact-registry/pricing
> https://cloud.google.com/container-registry/pricing

Vulnerability scanning - $0.26 per scanned container image.

Storage over 0.5 GB - $0.10 GB/month.

Network egress:

- within the same location - free.
- from a repository located in a region (multi-region) to a different Google Cloud service located in a region (multi-region), and both locations are on the same continent - free.
- between locations in US and Canada and none of the free traffic types apply - $0.01 per GB.

##### Verdict

It is a good product but in it's early stage. Golang, Python, Conda and Ruby are not supported. Proxy public packages not supported. Security scanning is limited. Reasonable choice for companies already using GCP, since it has a lot of integrations with different GCP services. Vendor lock.



### GitHub Packages

> https://docs.github.com/en/packages

- Supported clients and formats: JS, Ruby, Java, .NET, Docker (OCI).
- The permissions for packages are either repository-scoped or user/organization-scoped.
- Download activity.
- You restore the package within 30 days of its deletion.
- You can use the REST API, GraphQL API and Web UI to manage your packages.
- Use GitHub Actions to automatically publish or install a package from GitHub Packages (CI\CD).

##### Pricing

> https://github.com/features/packages#pricing

Enterprise ($21 per user/month):

- 50GB, additional - $0.25 per GB.
- Data transfer out within Actions - free.
- Data transfer out outside of Actions - 100GB per month, additional - $0.50 per GB.

##### Verdict

GitHub Packages is a good product and it is a part of GitHub itself which makes it very easy to use and without any maintenance.




### Sonartype Nexus

> https://help.sonatype.com/repomanager3
>
> https://www.sonatype.com/products/repository-oss-vs-pro-features

- Nexus Repository OSS (free) vs. Pro (SaaS, paid).
- Supported formats: APK, APT, Bower, Cargo, Chef, CocoaPods, Composer, Conan, Conda, CPAN, Docker, ELPA, Git, Go, Helm, Maven, npm, NuGet, p2, Puppet, PyPI, R, Raw, RubyGems, Yum.
- Authentication methods: SAML, Atlassian Crowd, LDAP.
- RBAC.
- Web UI, REST API, Webhooks support.
- Repository Health Check feature: provides a list of vulnerable components, available package upgrades, license issues.
- Can operate as caching proxy of remote repositories.
- Other features: high availability clustering, dynamic storage, staging and build promotion, tagging, auditing.

##### Pricing

> https://www.sonatype.com/products/pricing

- Nexus Repository Pro - $120 per user/year.

##### Verdict

It supports a lot of different artifacts types (in paid, SaaS version). But product feels outdated, built with old technologies, like Jenkins on steroids. Documentation is messy, with a lot of marketing in it.




### GitLab Packages and Registries

> https://docs.gitlab.com/ee/user/packages/

- Supports the following formats: Composer, Conan, Go, Helm, Maven, npm, NuGet, PyPI, Generic packages, Ruby gems, Terraform Modules, Docker + 11 community formats.
- Proxy for frequently-used upstream images and packages.
- Cleanup policies.

##### Pricing

> https://about.gitlab.com/pricing/

- Premium - $19 per user/month.
- Ultimate - $99 per user/month.

##### Verdict

Packages and Registries in deeply integrated in GitLab itself. GitLab is an awesome tool, but it doesn't make sense to use it only as Artifact Registry.




### CloudSmith

> https://help.cloudsmith.io/docs/welcome-to-cloudsmith-docs
> https://cloudsmith.io/~cloudsmith/repos/examples/packages/#

- Format support: Alpine Linux, Cargo (rust), Chocolatey, CocoaPods, Composer (php), Conan (C, C++), CRAN (R), Dart, Debian, `Docker`, `Go`, Gradle, `Helm Charts (Kubernetes)`, LuaRocks (Lua), Maven including support for Leiningen, `npm`, NuGet, `Python`, Raw, `Ruby`, RPM (RedHat), sbt, Terraform Modules, Unity, Vagrant.
- Only SaaS.
- CLI, REST API, Website UI, Webhooks.
- Upstream Proxying.
- Retention / Lifecycle policies.
- CI tooling integrations (AWS CodeBuild, Bitbucket Pipelines, Buildkite, CircleCI, Drone CI, GitHub Actions, Jenkins, Travis-CI).
- RBAC
- Security: encrypted in transit, encrypted at rest (KMS), additional GPG or RSA signatures can be used to detect tampering.
- Auditability: access logs, metrics/statistics, accountability for uploads and downloads.
- SSO supported providers: Azure AD, Google, JumpCloud, Okta, OneLogin.
- License Compliance
- Security Scanning (only in Ultra plan)

##### Pricing

> https://cloudsmith.com/product/pricing

Velocity - $299:

- Storage - 30 GB, additional $0.80 per GB.
- Bandwidth - 60 GB, additional $0.40 per GB.
- We will need a better plan (Ultra), since it supports security scanning, SSO/SAML etc.

##### Verdict

Cons:

- Upstream Proxying / Caching no supported for Golang yet.
- Only built-in security scanning tool can be used.
- Only SaaS.




### Dist

> https://docs.dist.cloud/

- Supports Docker containers and Maven artifacts only
- RBAC
- Only SaaS

##### Pricing

> https://www.dist.cloud/pricing

$10 month per user.

##### Verdict

Limited package technologies support.




### CloudRepo

> https://www.cloudrepo.io/docs/product-overview.html

- Maven and Python only (NPM, Helm support on roadmap)
- Can proxy remote repositories and download dependencies.
- Webhooks.
- User management (SSO compatible).
- Only SaaS.
- Compatible with CI tools (GitLab, CircleCI etc.)

##### Pricing

> https://www.cloudrepo.io/pricing.html

Growth - $150 per month.

##### Verdict

Limited package technologies support.



### Anchore

> https://anchore.com
> https://docs.anchore.com/current/docs/overview/

- Docker container static analysis and policy-based compliance system.
- SSO support (Okta, KeyCloak).
- Can run in an isolated environment with no outside internet connectivity.
- Support for using Role-Based Access Control (RBAC).
- Provide insightful analytics and metrics for account-wide artifacts.
- Runtime Compliance.
- UI, CLI, RESTful API support.

##### Pricing

> https://anchore.com/pricing

N/A.

##### Verdict

Anchore Enterprise is more container security solution (like Twistlock, Aquasec, etc.).



### Red Hat Quay

> https://www.redhat.com/en/resources/quay-datasheet

- Quay [builds, analyzes, distributes] your container images.
- Automatic security scanning.
- On premises and on cloud.
- Integration with identity infrastructure: LDAP, OIDC, OAuth, Keystone etc.
- Integration with GitHub, Bitbucket and more.
- Features and benefits: history of all tags, geographic replication, unlimited storage, automated builds, audit logging, HA, metrics, CI, BitTorrent distribution and more.

##### Pricing

> https://quay.io/plans

250 private repositories - $450 per month.

##### Verdict

Quay is a container registry.



### Perforce Helix Core

> https://www.perforce.com/products/helix-core

- Perforce version control system.
- Works very well for large binary files.
- Streams - superior branching and merging tool.
- Audit history,  OpenID Connect or SAML 2.0 support, rich access control.
- Swarm - code review tool.
- CLI and GUI tools, IDE plugins.

##### Pricing

> https://www.perforce.com/how-buy

N/A.

##### Verdict

Perforce Helix Core is a version control system.



### Pulp Project (v3)

> https://pulpproject.org
> https://docs.pulpproject.org/pulpcore/index.html

- Different content types: RPM, File, Container, Ansible, Debian, Python, Ruby Gem, Chef Cookbook, Maven.
- Extendable with custom content plugins.
- Versioning.
- Multiple storage options (AWS, Azure etc.)

##### Pricing

Free and open-source.

##### Verdict

Cons:

- still under development.
- no Go support.
- Niche, not very popular product.



### Apache Archiva

> https://archiva.apache.org/index.html

- Maven build artifact repository manager.
- Aggregate (proxy) content from remote artifact repositories.
- Visualize artifact utilization with search, browse and reporting.
- Security access management.

##### Pricing

Free and open-source.

##### Verdict

Maven only.



### ProGet

> https://docs.inedo.com/docs/proget-overview

- Packages support: NuGet, PowerShell, Ruby Gems, Visual Studio Extension (.vsix), Maven (Java), npm (Node.js), Chocolatey (Windows/Machine), Debian (apt), Helm (Kubernetes), PyPi (Python), RPM (yum), Docker (containers).
- Vulnerability scanning and license detection.
- Package Promotion (separating production-ready and production-unready packages).
- Package  Statistics.
- Free and enterprise editions.

##### Pricing

> https://inedo.com/proget/pricing

ProGet Basic $2000 per year.

##### Verdict

Looks like it was designed to be used with NuGet (.Net) packages mostly.



### MyGet

> https://docs.myget.org/

- Packages from NuGet, OneGet, Chocolatey, TeamCity, npmjs, Maven Central, Bower, Packagist, PyPI, RubyGems.org or any other package source.
- Can compile code, run tests, create artifacts, do vulnerability scanning and check license compliance.
- Supports package mirroring.
- Only SaaS

##### Pricing

> https://myget.org/pricing

Team - $440 per year.

##### Verdict

MyGet was designed to be used with NuGet (.Net) packages mostly.



### Athens

> https://docs.gomods.io
> https://github.com/gomods/athens

-  Proxy server for the Go modules download API.

##### Pricing

Free and open source.

##### Verdict

Popular project and it can be very useful for Golang projects.