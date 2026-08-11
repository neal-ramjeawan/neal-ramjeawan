<div align="center">

<img src="https://readme-typing-svg.demolab.com/?font=Fira+Code&size=20&duration=3000&pause=800&color=00ADB5&center=true&vCenter=true&width=650&lines=I+build+it%2C+break+it%2C+fix+it%2Cthen+automate+it.;Currently+shipping+on+an+M1+Air+with+8GB+RAM.;Open+to+Cloud+Engineer+%2F+DevOps+%2F+SRE+roles." alt="Typing SVG" />

[![GitHub](https://img.shields.io/badge/GitHub-222831?style=for-the-badge&logo=github&logoColor=EEEEEE)](https://github.com/neal-ramjeawan)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-00ADB5?style=for-the-badge&logo=linkedin&logoColor=white)](#)
[![Portfolio](https://img.shields.io/badge/Portfolio-393E46?style=for-the-badge&logo=vercel&logoColor=EEEEEE)](#)

</div>

<br>

## Most of this started as a question I couldn't answer at work

I'm Neal — 6+ years across AWS, Azure, Linux, and Windows, and my usual way of answering "how does X actually work" is to rebuild a smaller, breakable version of X until I know for certain.

A lot of that rebuilding happened on a MacBook Air with 8GB of RAM, which sounds like a limitation and mostly was — but it also meant every project had to be Docker-first, profile-gated, and honest about resource usage before it was allowed near a real cloud bill. That constraint shows up more than once below.

I'm currently looking at **Cloud Engineer, DevOps, and SRE / Systems Engineer** roles.

<br>

## What I'm building right now

<br>

### 🔐 Greendale Keystone — a miniature enterprise, built to see how the pieces actually fit
**[github.com/neal-ramjeawan/greendale-keystone](https://github.com/neal-ramjeawan/greendale-keystone)**

I wanted to stop learning identity, secrets, VPN, monitoring, and logging as separate tutorials and actually wire them together the way a real environment forces them to talk to each other. So: Samba4 AD-DC behind CoreDNS, WireGuard for remote access, Vault for secrets, Prometheus/Grafana/Loki for the "what just happened" layer, Traefik in front of all of it — each piece a gated Docker Compose profile so I could bring up only what I was actively working on.

The interesting part was never the happy path, it was everything that broke on the way there:
- Samba4 provisioning threw `ACCESS_DENIED (set_nt_acl_no_snum)` until I granted the container `privileged: true`
- WireGuard peer names have to be alphanumeric-only — a linuxserver/wireguard image quirk that cost me an afternoon
- Docker Desktop on Mac won't route host-to-container-bridge-IP the way Linux does, which broke my LDAP testing until I published the ports and pointed at `127.0.0.1` instead

The centerpiece is a self-service password reset flow: a Slack slash command, TOTP verified against a Vault-stored secret, an LDAP reset against AD, and a structured JSON audit log that lands in Loki — auto-approved, the same way a real reset would work, no second human in the loop.

`Docker` `Samba4 AD-DC` `WireGuard` `Vault` `Prometheus` `Grafana` `Loki` `Traefik` `Flask`

<br>

### 🏦 Legacy Bank HA Migration — from "it works on my machine" to chaos-tested HA
**[github.com/neal-ramjeawan/legacy-bank-ha-migration](https://github.com/neal-ramjeawan/legacy-bank-ha-migration)**

Starting point: a legacy Flask app on SQLite, single point of failure in every direction. End point: a Postgres-backed, gunicorn-served app on an HA Kubernetes cluster with anti-affinity, pod disruption budgets, rolling updates, NetworkPolicy, TLS via cert-manager, and pod security hardening — deployed through Helm and Argo CD.

Then I tried to break it on purpose:
- **Pod kills:** 0/200 failed requests
- **Node drain:** clean retarget, zero downtime
- **Rolling update under load:** 0/300 failed requests

CI runs lint, pytest, a Trivy scan, CodeQL, kubeconform, and a full kind smoke test on every push. I wrote up the real bugs I hit along the way — not the polished version, the actual debugging — in a full case-study post alongside the README.

`Flask` `PostgreSQL` `Kubernetes` `Helm` `Argo CD` `k6` `Trivy` `CodeQL` `GitHub Actions`

<br>

### 👀 CloudEye — what if ClickOps left a trail you could actually act on?

The idea started from a real annoyance: infra drift is invisible until it isn't. CloudEye watches AWS CloudTrail for management-plane activity, tells the difference between an approved IaC pipeline and someone in the console making a "quick fix," enriches the event with actor, resource, account, region, source IP, and severity, and pushes it to Slack instead of burying it in a log group nobody reads.

Next up: multi-account AWS Organizations support and configurable risk scoring, so alert volume scales with how much I actually trust the account it came from.

`CloudTrail` `EventBridge` `Lambda` `Terraform` `Slack`

<br>

### 🩹 Patch Compliance Dashboard — making SSM Patch Manager data mean something
Every decision on this one had a reason I could defend: DynamoDB over RDS to dodge VPC plumbing and idle cost, Streamlit Community Cloud over App Runner because a dashboard didn't need a container, `typing.Protocol` for dependency injection instead of pulling in a framework for a project this size. Backend is Lambda + EventBridge + DynamoDB, frontend is Streamlit, CI runs unit tests, ruff, and `terraform fmt` on every push.

`AWS Systems Manager` `Lambda` `EventBridge` `DynamoDB` `Streamlit` `Terraform`

<br>

## More in the lab

| Project | What it is | Status |
|---|---|---|
| **Identity Automation Platform** | Python IAM lifecycle automation — validation layer, audit logging, and a workflow engine with crash recovery | Building — API/UI phases next |
| **[Zwazo](https://github.com/neal-ramjeawan/do-you-know-mauritius)** | A trivia site that's *only* about Mauritius — 150+ questions across 8 categories, React/Vite/Tailwind | Shipped |
| **Mauritius Info Hub** | Public dashboard aggregating weather, power, water, and road alerts for Mauritius — Next.js/FastAPI/Postgres, built for a $0/month stack | Building V1 |
| **AWX Ansible Lab** | Ansible Tower (AWX) on minikube, GitHub-integrated playbook storage, CI via GitHub Actions | Packaged |
| **Keycloak IAM Demo** | Eight-phase IAM deep dive — deployment through M2M auth, fine-grained authz, LDAP federation, identity brokering, multi-realm, Terraform-managed config, Admin REST API scripting | Repo |
| **K8s / OpenShift Troubleshooting Labs** | Two repos, deliberately broken on purpose — five modules each of broken manifests, runbooks, and automated break/verify scripts | Repo |
| **AWS Transfer Family App** | SFTP user management shouldn't require touching the console by hand — Streamlit + FastAPI + boto3, IRSA for auth, tested against LocalStack | Built |

<br>

## How I actually work

**If I can't see it, I don't trust it.** Every project above got monitoring and logging before it got polish — Greendale Keystone had Grafana wired to both Prometheus and Loki before the password-reset feature even worked.

**Boring is the goal, not the starting point.** Nothing above was boring while I was building it. That's the point — the weird failure modes get found and fixed *before* something depends on it.

**Docs are part of the build.** I write mine in first person, as the person who actually made the decisions — DynamoDB over RDS, Streamlit over App Runner, `Protocol` over a DI framework — because "we chose X" without the reasoning is just trivia for the next engineer to rediscover the hard way.

**Break it on purpose, on my own schedule.** Chaos scripts, intentionally broken manifests, break/verify runbooks — I'd rather find the failure mode at 2pm on a Tuesday with nothing on the line than at 2am with something on fire.

<br>

## Toolbox

**Cloud**
![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat-square&logo=amazonaws&logoColor=white)
![Azure](https://img.shields.io/badge/Azure-0078D4?style=flat-square&logo=microsoftazure&logoColor=white)

**Infrastructure as Code**
![Terraform](https://img.shields.io/badge/Terraform-7B42BC?style=flat-square&logo=terraform&logoColor=white)
![OpenTofu](https://img.shields.io/badge/OpenTofu-FFDA18?style=flat-square&logo=opentofu&logoColor=222831)
![Ansible](https://img.shields.io/badge/Ansible-EE0000?style=flat-square&logo=ansible&logoColor=white)
![Packer](https://img.shields.io/badge/Packer-02A8EF?style=flat-square&logo=packer&logoColor=white)

**Containers & Platforms**
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=kubernetes&logoColor=white)
![Helm](https://img.shields.io/badge/Helm-0F1689?style=flat-square&logo=helm&logoColor=white)

**CI/CD & GitOps**
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white)
![Argo CD](https://img.shields.io/badge/Argo_CD-EF7B4D?style=flat-square&logo=argo&logoColor=white)

**Identity & Security**
![Vault](https://img.shields.io/badge/Vault-FFEC6E?style=flat-square&logo=vault&logoColor=222831)
![Keycloak](https://img.shields.io/badge/Keycloak-393E46?style=flat-square&logo=keycloak&logoColor=white)
![Active Directory](https://img.shields.io/badge/Active_Directory-00ADB5?style=flat-square)
![IAM](https://img.shields.io/badge/IAM-222831?style=flat-square)

**Systems**
![Linux](https://img.shields.io/badge/Linux-FCC624?style=flat-square&logo=linux&logoColor=222831)
![Windows Server](https://img.shields.io/badge/Windows_Server-0078D6?style=flat-square&logo=windows&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-5391FE?style=flat-square&logo=powershell&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)

<br>

<div align="center">

![Followers](https://img.shields.io/github/followers/neal-ramjeawan?style=for-the-badge&color=00ADB5&labelColor=222831&label=FOLLOWERS)
![Greendale Keystone](https://img.shields.io/github/stars/neal-ramjeawan/greendale-keystone?style=for-the-badge&color=00ADB5&labelColor=222831&label=%E2%98%85%20GREENDALE%20KEYSTONE)
![Legacy Bank HA](https://img.shields.io/github/stars/neal-ramjeawan/legacy-bank-ha-migration?style=for-the-badge&color=00ADB5&labelColor=222831&label=%E2%98%85%20LEGACY%20BANK%20HA)

</div>

<br>

## On the radar

Cloud, security, and platform engineering meeting AI agents is where I'm spending more of my time — not as a buzzword, I've been prototyping a Confluence MCP server and a PagerDuty + Confluence incident-postmortem pipeline to see what agentic SRE tooling actually looks like day to day. Also circling: eBPF-based observability, policy as code, and cloud detection engineering.

Some of this will become the next project above. Some of it won't go anywhere. I keep both kinds of notes.

<br>

<div align="center">

---

**Let's talk if you're hiring for Cloud, DevOps, or SRE — or if you just want to argue about whether infra should ever be exciting.**

</div>
