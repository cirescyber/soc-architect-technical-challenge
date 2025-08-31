# SOC Architect – Technical Challenge

**Role Target:** SOC Architect / DevOps  
**Estimated Effort:** ~16–20 focused hours (varies by experience)  

---

## 1. Scenario

You are tasked with building a **Mini SOC platform** leveraging Wazuh SIEM, container orchestration, and DevOps practices. The solution must be automated, secure, and reproducible via a CI/CD pipeline.  

In addition, you will extend Wazuh with a **custom detection rule** to identify brute-force SSH attempts followed by suspicious logins, simulating real SOC operations.  

---

## 2. Deliverables

### Part One – Build a Mini SOC

1. **Wazuh Stack**
   * Deploy Wazuh Indexer, Manager, and Dashboard on **Docker Swarm**  
   * Expose the dashboard via **HTTPS**  
   * Persist data with Docker volumes  
   * Deploy multi-node topology for bonus points  

2. **CI/CD Pipeline (GitHub Actions + Self-Hosted Runners)**
   * On PR and push to `main`:  
     - Build container images  
     - Scan with **Trivy** (fail on Critical/High findings)  
     - Run **Selenium tests**  
     - Deploy to Swarm via **Ansible** (only after tests pass on `main`)  
   * Document runner prerequisites (Docker, Python, Ansible, Trivy, Chrome/Playwright)  

3. **Testing**
   * Selenium test cases:  
     - HTTPS dashboard availability  
     - Page title and login form presence  
     - Programmatic login with test credentials  
   * API health probe: Wazuh Manager endpoint returns 200/valid JSON  

4. **Deployment (Ansible → Docker Swarm)**
   * Initialize Swarm (idempotent)  
   * Create overlay networks, configs, secrets  
   * Deploy/update stack with rollback support  

5. **Security & Secrets**
   * No secrets in Git or plaintext YAML  
   * Use one or more:  
     - GitHub Actions Secrets + Swarm secrets  
     - Ansible Vault  
     - HashiCorp Vault or SOPS  
   * Document rotation approach and least-privilege  

6. **Certificates**
   * HTTPS via Nginx/Traefik with **Let’s Encrypt ACME** or internal CA  
   * Automate renewal where possible  

7. **Quality Gates**
   * Trivy: fail on Critical/High vulnerabilities  
   * Lint with `ansible-lint`, `yamllint`  
   * Require PR checks before merge  

---

### Part Two – Threat Incident Scenario

1. **Pattern to Detect**
   * Multiple failed SSH logins from same IP  
   * Followed by a successful login with a **new username**  

2. **Implementation**
   * Custom Wazuh rule (ID ≥ 100000) in `local_rules.xml`  
   * Decoder if needed (extract `srcip`, `user`)  
   * Map to MITRE ATT&CK **T1110 – Brute Force**  

3. **Testing**
   * Inject simulated `auth.log` entries with `logger`  
   * Confirm alert is generated in dashboard and JSON event  
   * Commit rule XML to repo  

4. **Bonus**
   * Geo-IP enrichment  
   * Alert forwarding to Slack/Teams  
   * SOAR playbook auto-block of source IP  

---

## 3. Evidence

* Screenshots of:  
  - Successful CI run  
  - Wazuh dashboard
  - Trivy scan results  
  - Ansible deploy logs  
* Sample custom Wazuh alert (JSON output)  
* Repository should include:  
  - `.github/workflows/` pipeline configs  
  - `ansible/` playbooks and roles  
  - `stack/` Swarm definitions  
  - `tests/` with Selenium and API probes  
  - `trivy/` configuration  

---

## 4. Optional Extensions

* **High Availability (HA):** Multi-node Swarm cluster with replicated Wazuh services  
* **Docs:** High-Level Design (HLD) and Low-Level Design (LLD) in `/docs` with diagrams  
* **Disaster Recovery (DR):** Snapshot, restore, and demo steps  
* **Update & Migration:** Rolling upgrades, migration scenarios, smoke tests  

---

## 5. Submission

1. Push your code, configs, and documentation to a **GitHub repo** (public or invite [@toufik-airane](https://github.com/toufik-airane))  
2. Include screenshots and outputs under `/results` or embed in README  
3. Submission must include:  
   * README with architecture overview and run instructions  
   * CI/CD workflows, Ansible configs, Docker stack files, tests  
   * Evidence artifacts (pipeline, dashboard, scans, alerts)  
4. Reach out to Toufik Airane at t.airane@cirestechnologies.ma  

---

## Final Note

You are **not required** to complete every part. Submit as much as you can with emphasis on **clarity, security, and real-world practicality.** The evaluation focuses on technical depth, quality of implementation, and communication of your design.

**Good luck and have fun.**
