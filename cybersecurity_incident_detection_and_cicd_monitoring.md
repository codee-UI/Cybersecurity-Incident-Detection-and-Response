# Cybersecurity Incident Detection and CI/CD Threat Monitoring

## Study Notes, Technical Analysis, Detection Workflows, and Practical Checklist

> **Purpose:** Consolidate and analyze the supplied learning material on
> cybersecurity incident detection, threat hunting, threat intelligence,
> cyber deception, and continuous monitoring of Continuous Integration
> and Continuous Delivery/Deployment (CI/CD) pipelines.
>
> **Audience:** Beginner-to-intermediate cybersecurity learners, SOC
> analysts, security engineers, DevSecOps practitioners, and students
> building a practical security portfolio.
>
> **Core principle:** Use multiple complementary detection methods. An
> alert is a lead to investigate---not automatic proof of
> compromise---and the absence of an alert is not proof that an
> environment is safe.

------------------------------------------------------------------------

## Table of Contents

1.  [Learning Objectives](#1-learning-objectives)
2.  [Incident Detection and
    Analysis](#2-incident-detection-and-analysis)
3.  [Core Detection Methods](#3-core-detection-methods)
4.  [Threat Hunting](#4-threat-hunting)
5.  [Threat Intelligence](#5-threat-intelligence)
6.  [Cyber Deception and Honeypots](#6-cyber-deception-and-honeypots)
7.  [CI/CD Security Monitoring](#7-cicd-security-monitoring)
8.  [CI/CD Indicators of Compromise and
    Attack](#8-cicd-indicators-of-compromise-and-attack)
9.  [Automated Monitoring
    Architecture](#9-automated-monitoring-architecture)
10. [Detection Rules and Example
    Scenarios](#10-detection-rules-and-example-scenarios)
11. [Incident Triage and Response
    Workflow](#11-incident-triage-and-response-workflow)
12. [Practical Lab: Review CI/CD
    Events](#12-practical-lab-review-cicd-events)
13. [Metrics and Evaluation](#13-metrics-and-evaluation)
14. [Common Mistakes and Technical
    Clarifications](#14-common-mistakes-and-technical-clarifications)
15. [Implementation Checklist](#15-implementation-checklist)
16. [Glossary](#16-glossary)
17. [Knowledge Check](#17-knowledge-check)
18. [References and Further
    Learning](#18-references-and-further-learning)

------------------------------------------------------------------------

## 1. Learning Objectives

After studying this document, you should be able to:

-   Distinguish **detection** from **analysis**, investigation, and
    incident response.
-   Explain the roles of an Intrusion Detection System (IDS) and
    Security Information and Event Management (SIEM) platform.
-   Describe threat hunting, threat intelligence, threat intelligence
    platforms, and cyber deception.
-   Identify suspicious behavior in source-code repositories, build
    systems, dependency management, secrets handling, and deployments.
-   Distinguish an **Indicator of Compromise (IoC)** from an **Indicator
    of Attack (IoA)** and from a vulnerability finding.
-   Design a basic monitoring workflow that combines logs, analytics,
    rules, threat intelligence, and human review.
-   Triage an alert without treating every anomaly as a confirmed
    incident.
-   Evaluate detection coverage using measurable metrics.

------------------------------------------------------------------------

## 2. Incident Detection and Analysis

Detection and analysis are closely related but have different purposes.

-   **Detection** is the prompt identification of a potentially
    security-relevant event or behavior.
-   **Analysis** is the investigation used to determine what the event
    means, whether it is malicious, what systems or accounts are
    affected, and how confident the team should be.
-   **Incident response** is the coordinated action taken when an event
    is confirmed or sufficiently suspected to require containment and
    remediation.

A common high-level incident response lifecycle is:

``` text
Prepare
  |
  v
Detect and Analyze
  |
  v
Contain, Eradicate, and Recover
  |
  v
Post-Incident Activity / Lessons Learned
  |
  +--------------------> Improve preparation and detection
```

Exact phase names vary between incident-response frameworks, but the
operational principle is consistent: detection begins an investigation;
it does not complete one.

### 2.1 Example

A SIEM alerts that a build runner connected to an unfamiliar external
domain.

1.  **Detection:** The network event matches a rule or anomaly
    threshold.
2.  **Triage:** An analyst checks the timestamp, destination, runner
    identity, and associated build.
3.  **Analysis:** The analyst reviews process execution, DNS records,
    egress logs, code changes, and the build's dependency downloads.
4.  **Decision:** The connection is explained by an approved package
    repository, or evidence indicates suspicious activity.
5.  **Response:** If compromise is suspected, the team may isolate the
    runner, revoke exposed credentials, preserve evidence, and
    investigate downstream artifacts.
6.  **Improvement:** The detection rule, allowlist, logging, and
    pipeline permissions are updated as appropriate.

### 2.2 Why one detection method is not enough

Tools only see the events and data sources available to them, and their
detection logic only covers what has been configured and tested. Common
gaps include:

-   Missing or disabled logs.
-   Incorrect timestamps or inconsistent time zones.
-   Rules that are too narrow, too broad, or outdated.
-   Encrypted traffic that is not observable at the selected monitoring
    point.
-   Fileless or memory-resident behavior that does not create a
    conventional file artifact.
-   Compromised legitimate accounts whose activity initially resembles
    normal work.
-   New attacker techniques not represented in existing signatures.
-   Alert overload that causes important signals to be missed.

**Recommended approach:** combine endpoint, identity, network,
source-control, build, dependency, and deployment telemetry where
feasible. Use automated detection for scale and human investigation for
context.

------------------------------------------------------------------------

## 3. Core Detection Methods

### 3.1 Intrusion Detection System (IDS)

An **IDS** monitors network traffic, host activity, or both for
suspicious patterns and generates alerts.

Common approaches include:

-   **Signature-based detection:** Matches activity to known patterns,
    signatures, or rules.
-   **Anomaly-based detection:** Identifies deviations from an
    established baseline of expected behavior.
-   **Behavior-based detection:** Looks for sequences or combinations of
    actions associated with suspicious behavior.

An IDS commonly alerts; an Intrusion Prevention System (IPS) may also
block or disrupt traffic inline. Product capabilities differ, so verify
how a particular tool is deployed.

**Strengths** - Can identify known attack patterns quickly. - Provides a
focused stream of security alerts. - Can add visibility to network or
host activity.

**Limitations** - Signatures may miss new or modified attacks. - Anomaly
detection can produce false positives. - Coverage depends on sensor
placement, data quality, configuration, and encrypted-traffic
visibility.

### 3.2 Security Information and Event Management (SIEM)

A **SIEM** centralizes and correlates security events from multiple
systems. Typical data sources include identity providers, endpoints,
firewalls, DNS, cloud services, source-control platforms, and CI/CD
systems.

A SIEM can support:

-   Log collection and normalization.
-   Correlation rules across multiple event sources.
-   Alerting and dashboards.
-   Investigation timelines and searches.
-   Retention and reporting.
-   Analytics or machine-learning-assisted anomaly detection, depending
    on the platform.

A SIEM is not automatically intelligent merely because it collects logs.
Its value depends on useful telemetry, sensible rules, context, tuning,
and a response process.

### 3.3 Detection method comparison

  -------------------------------------------------------------------------------------
  Method                 Main purpose   Typical input    Example signal Important
                                                                        limitation
  ---------------------- -------------- ---------------- -------------- ---------------
  Signature/rule-based   Find known     Logs, files,     Known          Can miss novel
  detection              patterns       network events   malicious hash or altered
                                                                        behavior

  Anomaly detection      Find           Historical and   Build duration Unusual does
                         deviations     current events   suddenly       not always mean
                         from baseline                   increases      malicious

  Correlation            Connect        Multiple data    New login      Requires
                         related events sources          followed by    reliable
                                                         secret access  identity and
                                                         and production time
                                                         deployment     correlation

  Threat hunting         Proactively    Logs, endpoint   Search for     Requires
                         search for     telemetry,       unusual        analyst time
                         hidden threats intelligence,    credential use and testable
                                        hypotheses       across runners hypotheses

  Threat intelligence    Add adversary  Reports,         Destination    Indicators can
                         and indicator  advisories,      matches a      be stale or
                         context        feeds            recently       incorrectly
                                                         reported C2    attributed
                                                         domain         

  Cyber deception        Make           Decoy accounts,  Access to a    Decoys must be
                         unauthorized   systems, files,  monitored      safely isolated
                         interaction    tokens           decoy          and monitored
                         observable                      credential     

  Continuous             Find           Source,          Vulnerable     A vulnerability
  vulnerability scanning exploitable    dependencies,    package        finding is not,
                         weaknesses     images,          version        by itself,
                                        infrastructure                  evidence of
                                                                        compromise
  -------------------------------------------------------------------------------------

------------------------------------------------------------------------

## 4. Threat Hunting

**Threat hunting** is the proactive, analyst-led search for signs of
malicious activity that automated alerts may have missed. It can also
deepen an investigation that began with an alert.

Threat hunting combines human reasoning with technical evidence. Hunters
formulate hypotheses, identify relevant data, search for patterns,
investigate results, and document findings.

### 4.1 Why threat hunting matters

Automated controls are essential, but attackers may:

-   Use legitimate administrative tools.
-   Operate through compromised accounts.
-   Avoid writing obvious files to disk.
-   Change infrastructure, domains, or malware to evade signatures.
-   Move slowly to remain below alert thresholds.
-   Exploit gaps between separate monitoring tools.

For example, fileless malware may execute in memory or abuse legitimate
scripting tools. Traditional file-hash scanning alone may not detect it.
Endpoint behavior telemetry, process relationships, command-line
activity, memory analysis, identity events, and network evidence can
provide additional signals.

### 4.2 A repeatable threat-hunting workflow

``` text
1. Define a hypothesis
        |
        v
2. Identify required data sources
        |
        v
3. Query and correlate telemetry
        |
        v
4. Validate suspicious findings
        |
        +---- Benign explanation? ----> Document and tune
        |
        v
5. Suspected/confirmed malicious activity
        |
        v
6. Escalate, preserve evidence, and respond
        |
        v
7. Create or improve detections
        |
        v
8. Record lessons and repeat
```

### 4.3 Example hunting hypotheses

-   A CI runner may be using credentials outside its normal pipeline
    scope.
-   A developer account may be making repository changes from an unusual
    device or location.
-   A build process may be contacting a domain unrelated to approved
    dependencies.
-   A production deployment may have occurred without the expected
    approval.
-   A newly introduced dependency may be downloaded from an unapproved
    registry.
-   A secret may have been printed into a build log or committed to
    source control.

A good hypothesis is specific enough to test. It should identify a
behavior, a data source, and the evidence that would support or weaken
it.

### 4.4 Threat-hunting inputs

Threat hunters may use:

-   **Threat intelligence:** Context about adversaries, campaigns,
    tactics, techniques, and indicators.
-   **Indicators of Compromise (IoCs):** Observable artifacts associated
    with possible or known compromise.
-   **Indicators of Attack (IoAs):** Behaviors or sequences that may
    indicate an attack in progress.
-   **Endpoint and network telemetry:** Process creation,
    authentication, DNS, connections, and other activity.
-   **Analytics and machine learning:** Prioritization or anomaly
    discovery, with human validation.

Machine learning can help prioritize or detect unusual behavior, but it
does not eliminate the need for good data, testing, explainability
appropriate to the use case, and analyst review.

------------------------------------------------------------------------

## 5. Threat Intelligence

**Threat intelligence** is evidence-based information about existing or
emerging threats, enriched with context so defenders can make decisions.
Useful intelligence helps answer questions such as: Who may be targeting
us? What techniques are being used? Which assets are exposed? What
evidence should we look for?

### 5.1 Common sources

1.  **Industry and research reports:** May describe attacker tactics,
    techniques, procedures (TTPs), campaigns, and defensive
    recommendations.
2.  **Government and sector advisories:** Provide alerts, vulnerability
    information, and mitigation guidance.
3.  **Threat-data feeds:** Frequently provide machine-readable
    indicators such as IP addresses, domains, URLs, and file hashes.
4.  **Internal telemetry:** An organization's own incident records,
    detections, and observations can be highly relevant.
5.  **Information-sharing communities:** May provide sector-specific
    context, subject to membership, trust, and handling rules.

### 5.2 Advanced Persistent Threat (APT)

An **Advanced Persistent Threat (APT)** generally describes a capable,
persistent threat actor or campaign that seeks to maintain access and
pursue objectives over time. The phrase is not simply a synonym for any
long-running login. Classification should be based on evidence and the
relevant source's definition.

### 5.3 Threat Intelligence Platform (TIP)

A **Threat Intelligence Platform (TIP)** collects, normalizes, enriches,
relates, and helps manage intelligence from multiple sources.

A TIP can help teams:

-   Deduplicate and enrich indicators.
-   Track provenance, confidence, and timestamps.
-   Prioritize intelligence relevant to their environment.
-   Share indicators with supported security tools.
-   Manage indicator expiration and review.
-   Connect indicators to campaigns, techniques, and internal
    observations.

### 5.4 Safe use of indicators

Threat feeds should **add context**, not automatically determine every
detection or response decision.

Before acting on an indicator, consider:

-   **Source reliability:** Who provided it, and how was it collected?
-   **Confidence:** How strongly is it associated with malicious
    activity?
-   **Freshness:** Is it still current?
-   **Context:** Could the IP address, domain, or hash have a legitimate
    use?
-   **Relevance:** Does the indicator relate to the organization's
    systems and threat model?
-   **Action impact:** Could blocking it disrupt a legitimate service?
-   **Expiration:** When should the indicator be reviewed or removed?

An IP address or domain can be shared by many services or change
ownership. A match is a lead to investigate, not conclusive proof that a
device is compromised.

------------------------------------------------------------------------

## 6. Cyber Deception and Honeypots

**Cyber deception** uses decoys and controlled misleading artifacts to
make malicious activity easier to detect, observe, or investigate.

A **honeypot** is a decoy system or service designed to attract and
record unauthorized interaction. Other deception mechanisms can include
decoy files, canary credentials, and monitored tokens.

### 6.1 Example: decoy document

An organization places a clearly monitored decoy document in a
restricted test environment. The document appears to contain sensitive
business information but contains no real customer data. Access triggers
a security alert and records relevant telemetry.

A decoy is useful only if its access events are monitored and routed to
someone who can investigate them.

### 6.2 Benefits

-   Can reveal access to resources that ordinary users should not need.
-   Can provide early warning of reconnaissance or unauthorized access.
-   Can produce high-value signals when the decoy is unlikely to be
    accessed during normal work.
-   Can help defenders study attacker behavior in a controlled
    environment.

### 6.3 Safety and deployment considerations

-   Do not place real customer data or production secrets in decoys.
-   Isolate honeypots and tightly restrict their connectivity.
-   Log access securely and synchronize timestamps.
-   Define an owner and response procedure for each alert.
-   Ensure the decoy cannot be used to attack other systems.
-   Obtain appropriate authorization and follow legal, privacy, and
    organizational policies.

Deception should complement---not replace---patching, access control,
logging, and other preventive and detective controls.

------------------------------------------------------------------------

## 7. CI/CD Security Monitoring

### 7.1 What is CI/CD?

-   **Continuous Integration (CI):** Developers frequently merge code
    changes, which are automatically built and tested.
-   **Continuous Delivery:** Software is kept in a releasable state,
    with release to production generally requiring an explicit decision
    or approval.
-   **Continuous Deployment:** Changes that pass required checks are
    automatically deployed to production.

Organizations use these terms somewhat differently, but the security
concerns are similar: source code, identities, dependencies, build
runners, secrets, artifacts, and deployment permissions must be
protected.

### 7.2 Why CI/CD pipelines are valuable targets

A compromised pipeline can affect software before it reaches users. An
attacker might attempt to:

-   Insert unauthorized code.
-   Steal signing keys, deployment tokens, or cloud credentials.
-   Modify build workflows.
-   Replace or tamper with software artifacts.
-   Introduce malicious dependencies.
-   Deploy to an unauthorized environment.
-   Disable or bypass security checks.
-   Use a build runner as a foothold into other systems.

CI/CD security is therefore part of **software supply-chain security**.

### 7.3 What should be monitored?

  -----------------------------------------------------------------------
  Area                    Useful telemetry        Suspicious examples
  ----------------------- ----------------------- -----------------------
  Source control          Commit, pull request,   Unexpected author,
                          branch protection,      bypassed review,
                          permission-change and   protected-branch change
                          audit logs              

  Identity and access     Login, MFA, token,      New location/device,
                          role, and permission    repeated failures then
                          events                  success, privilege
                                                  escalation

  Build execution         Job start/end, runner   Unknown job, unusual
                          identity, commands,     command, unexpected
                          workflow changes,       workflow modification
                          artifacts               

  Dependencies            Lockfile changes,       New unapproved package,
                          registry access,        registry change,
                          package provenance,     suspicious version
                          vulnerability scan      
                          results                 

  Secrets                 Secret scanning,        Secret in source or
                          secret-store access,    logs, unexpected secret
                          masking events, audit   retrieval
                          logs                    

  Network egress          DNS, proxy, firewall,   Runner connects to an
                          flow and endpoint       unrelated or known
                          events                  malicious destination

  Deployment              Release approvals,      Unapproved production
                          artifact identity,      release or unexpected
                          target environment,     target
                          deployment actor        

  Infrastructure          Configuration changes,  Logging disabled,
                          runner image changes,   monitoring agent
                          container and host      removed, unexpected
                          events                  privileged runner
  -----------------------------------------------------------------------

### 7.4 Baseline normal behavior

Monitoring systems can compare current pipeline activity against
historical behavior. A baseline may include:

-   Typical duration for each stage.
-   Expected job success and failure rates.
-   Normal deployment times and frequencies.
-   Expected actors, runner pools, and target environments.
-   Approved dependency registries and network destinations.
-   Expected pipeline step order and permissions.

A deviation can be useful, but **an anomaly is not necessarily an
attack**. A legitimate large build, a planned release, a new dependency,
or a temporary service outage can cause unusual behavior. Rules should
consider context and route alerts for validation.

Baselines should be updated deliberately. Automatically learning every
new behavior without review can cause an attacker's activity to become
part of the supposed "normal" pattern.

------------------------------------------------------------------------

## 8. CI/CD Indicators of Compromise and Attack

The terms IoC and IoA are related but not interchangeable.

-   **Indicator of Compromise (IoC):** An observable artifact or
    evidence that may be associated with compromise, such as a known
    malicious hash, a suspicious domain, or an unauthorized persistence
    artifact.
-   **Indicator of Attack (IoA):** A behavior or sequence that suggests
    an attack may be occurring, such as attempts to extract secrets
    followed by unusual outbound traffic.
-   **Anomaly:** A deviation from expected behavior. It may be benign,
    malicious, or unexplained.
-   **Vulnerability finding:** Evidence of a weakness, such as a
    dependency with a known CVE. It is a security risk, but not
    automatically evidence that compromise occurred.

### 8.1 CI/CD signal catalogue

  ------------------------------------------------------------------------
  Signal category   Example            Classification    Suggested
                    observation                          investigation
  ----------------- ------------------ ----------------- -----------------
  Unauthorized code Unapproved user    IoA / suspicious  Review identity,
  changes           modifies a         event; may become commit,
                    workflow file      compromise        approvals, diff,
                                       evidence          and audit trail

  Unexpected commit Protected-branch   Anomaly           Verify the
  timing            change outside                       author, change
                    normal release                       request, and
                    window                               release plan

  Suspicious code   Obfuscated command IoA / suspicious  Inspect the diff
  content           added to build     behavior          and execution
                    workflow                             context in a safe
                                                         environment

  Unapproved        Production         IoA / policy      Check actor,
  deployment        deployment         violation         token, artifact,
                    bypasses required                    target, and
                    approval                             approval records

  Unknown external  Runner connects to Anomaly;          Correlate DNS,
  destination       a new domain       potentially IoC   process, job,
                    unrelated to build when supported by threat
                    needs              evidence          intelligence, and
                                                         proxy logs

  Known malicious   Artifact matches a IoC               Verify artifact
  hash              well-supported                       provenance,
                    malicious hash                       affected jobs,
                                                         and downstream
                                                         deployments

  New dependency    Unplanned package  Anomaly /         Review package
                    appears in         supply-chain risk owner, source,
                    lockfile                             version,
                                                         provenance, and
                                                         code changes

  Vulnerable        Scanner identifies Vulnerability     Assess version,
  dependency        a dependency with  finding           exploitability,
                    a CVE                                exposure, and
                                                         remediation
                                                         priority

  Repeated build    Many failures      Anomaly /         Check change
  failures          begin after a      possible IoA      history, runner
                    workflow change                      health,
                                                         dependency
                                                         availability, and
                                                         logs

  Secret in source  A credential is    Secret exposure;  Revoke/rotate the
  or logs           detected in a      potential         credential,
                    commit or build    compromise risk   assess access,
                    output                               and remove
                                                         exposure safely

  Unusual secret    Runner retrieves a IoA / suspicious  Validate
  access            production secret  event             identity, job
                    outside its                          permissions,
                    expected job                         audit events, and
                                                         downstream use

  Monitoring        Logging or         IoA /             Check approvals,
  disabled          security scan step defense-evasion   actor,
                    is removed         concern           configuration
                    unexpectedly                         history, and
                                                         surrounding
                                                         activity
  ------------------------------------------------------------------------

**Important:** classification depends on context. A suspicious
observation can be an IoC, IoA, anomaly, policy violation, or
vulnerability finding depending on what the evidence actually shows.

### 8.2 Unusual pipeline execution

Examples worth investigating include:

-   A job runs on an unexpected runner or with an unexpectedly
    privileged identity.
-   A workflow's step order changes without an approved change.
-   A pipeline takes far longer than its historical range.
-   Security tests are skipped, disabled, or made non-blocking.
-   A build unexpectedly requests access to production secrets.
-   A release artifact differs from the artifact that passed testing.

### 8.3 Secret exposure response

If a secret is found in code, logs, or an artifact:

1.  Treat it as exposed; do not assume deletion makes it safe.
2.  Revoke or rotate the credential using the approved process.
3.  Determine where it was used and whether it was accessed.
4.  Remove the secret from active code and logs where feasible, while
    preserving evidence and complying with retention requirements.
5.  Review repository history and derived artifacts.
6.  Add preventive controls such as secret scanning, least-privilege
    credentials, short-lived tokens, and log masking.
7.  Document the incident and verify that the replacement credential is
    not exposed.

------------------------------------------------------------------------

## 9. Automated Monitoring Architecture

A practical architecture collects relevant telemetry, enriches it,
applies detection logic, and routes alerts into an investigation
process.

``` text
  Source Control      CI/CD Platform       Identity Provider
       |                    |                    |
       v                    v                    v
  Commit/Audit Logs    Build/Deploy Logs    Login/Token Logs
       \                    |                    /
        \                   |                   /
         +------------------+------------------+
                            |
                            v
               Log Collection / Normalization
                            |
                            v
          Enrichment: Asset, User, Job, Threat Context
                            |
                            v
        +-------------------+--------------------+
        |                   |                    |
        v                   v                    v
   Rule/Signature       Baseline and         Correlation
     Detection         Anomaly Analysis       Analytics
        |                   |                    |
        +-------------------+--------------------+
                            |
                            v
                   Alert Prioritization
                            |
                            v
                 Analyst Triage / Hunting
                            |
                +-----------+-----------+
                |                       |
                v                       v
           Benign / Expected      Suspected Incident
                |                       |
                v                       v
         Document / Tune       Contain / Investigate
                                        |
                                        v
                             Recover / Improve Controls
                                        |
                                        v
                              Detection Feedback Loop
```

### 9.1 Comprehensive logging and auditing

**Pipeline execution logs** - Record job start/end, step results, runner
identity, and relevant command metadata. - Compare stage durations and
failure patterns against a reasonable baseline. - Avoid recording
credentials or other sensitive values.

**Code commit and repository audit logs** - Track authors, reviewers,
branch changes, permission changes, and workflow modifications. - Alert
on protected-branch or review-policy bypasses. - Correlate commit
identity with identity-provider events where possible.

**Access logs** - Record authentication, MFA outcomes, token
creation/use, role changes, and access to sensitive settings. -
Investigate unusual locations or devices in context; location alone is
not conclusive evidence.

**Deployment logs** - Capture who or what initiated a deployment,
approvals, artifact identity, environment, result, and timestamp. -
Compare production deployments with approved change records and release
policies.

**Network logs** - Record DNS queries, proxy events, firewall decisions,
and outbound connections where appropriate. - Correlate suspicious
destinations with the process, runner, build job, and dependency
operation that initiated them.

### 9.2 SIEM integration

Forward relevant CI/CD events to a SIEM or equivalent analytics
platform. Useful capabilities include:

-   Parsing and normalization of events from different platforms.
-   Correlation across identity, source control, build, network, and
    deployment logs.
-   Rules for known suspicious patterns.
-   Baseline or anomaly analytics.
-   Case management, alert ownership, and escalation.
-   Searchable historical data for threat hunting.

**Example rule concepts** (adapt to the actual platform and schema):

-   Alert when a production deployment occurs without a required
    approval record.
-   Alert when a workflow file changes and the change bypasses the
    normal review process.
-   Alert when a runner accesses a sensitive secret outside its approved
    job context.
-   Alert when a runner contacts a well-supported malicious destination.
-   Alert when a credential is used from an unusual context and is
    followed by a privileged action.

Do not copy example logic into production without testing field
mappings, exceptions, thresholds, and expected behavior.

### 9.3 Real-time alerting

Prioritize alerts based on a combination of:

-   Asset criticality.
-   Account and credential privileges.
-   Confidence in the signal.
-   Evidence of active exploitation.
-   Potential blast radius.
-   Whether the event affects production or software distribution.
-   Whether independent data sources corroborate the event.

Not every unusual build needs an emergency page. High-confidence secret
theft, unauthorized production deployment, or a compromised release
artifact generally deserves faster escalation than a minor unexplained
duration increase.

### 9.4 Performance monitoring as a security signal

Performance monitoring is primarily intended to keep services reliable,
but it can reveal symptoms worth investigating:

-   Unexpected CPU, memory, or network use on a runner.
-   Unexplained build duration changes.
-   Repeated failures after a configuration change.
-   Sudden resource exhaustion.
-   Unexpected processes or network traffic associated with a job.

These are often **anomalies or possible indicators of attack**, not IoCs
by themselves. Correlate them with security telemetry before concluding
that an attack occurred.

### 9.5 Continuous vulnerability scanning

Scan relevant components regularly, including:

-   CI/CD servers, plugins, and runners.
-   Source-code dependencies.
-   Container images and base images.
-   Infrastructure-as-code and deployment configuration.
-   Operating systems and build tools.

A CVE finding identifies a known vulnerability. Prioritize remediation
using severity, exploitability, exposure, available mitigations, and the
role of the affected component. A vulnerable component is not
automatically compromised, but an exposed, exploitable component may
create a significant path to compromise.

------------------------------------------------------------------------

## 10. Detection Rules and Example Scenarios

The examples below are conceptual. Adapt them to your actual event
schema and platform. Avoid placing live credentials, real customer data,
or sensitive internal details in test logs.

### Scenario A: Unapproved production deployment

**Hypothesis:** Every production deployment should be linked to an
approved release.

**Potential data sources:** deployment audit logs, change-management
records, identity logs, source-control audit events.

**Detection logic:** 1. Identify a production deployment event. 2.
Extract the actor, artifact identifier, target environment, and
timestamp. 3. Check for the required approval record and
policy-compliant workflow. 4. Alert if approval is missing or the
deployment bypassed the approved path.

**Triage questions:** - Was there an emergency change or approved
exception? - Did the deployed artifact pass required tests? - Who
initiated the action, and which credential was used? - Was the workflow
or permission configuration changed shortly beforehand?

### Scenario B: Suspicious outbound connection from a runner

**Hypothesis:** Build runners should communicate only with approved
destinations required for their jobs.

**Potential data sources:** DNS, proxy/firewall, endpoint process
events, build logs, dependency registry logs, threat intelligence.

**Detection logic:** 1. Identify the runner and external destination. 2.
Compare the destination with approved registries and documented
dependencies. 3. Enrich the destination with reputation and intelligence
context. 4. Correlate the connection with process and job activity. 5.
Escalate when multiple signals support suspicious behavior.

**Caution:** New does not automatically mean malicious. Package
registries and cloud services can use changing infrastructure.

### Scenario C: Unexpected secret access

**Hypothesis:** A job should access only the secrets explicitly required
for its task.

**Potential data sources:** secret manager audit logs, CI/CD job logs,
identity/token events, deployment logs.

**Detection logic:** 1. Record secret access by identity, job,
repository, and time. 2. Compare access with the job's expected
permissions. 3. Alert on access outside the approved scope or unexpected
retrieval patterns. 4. If exposure is plausible, revoke or rotate the
secret and investigate its use.

### Scenario D: Suspicious workflow change

**Hypothesis:** Changes to pipeline definitions require review and
should not disable required security controls.

**Potential data sources:** repository audit logs, pull request
approvals, workflow diffs, pipeline results.

**Detection logic:** 1. Monitor changes to workflow and build
configuration files. 2. Identify changes to permissions, secret access,
external actions, runner configuration, and security steps. 3. Verify
review and approval requirements. 4. Alert on unapproved changes or
attempts to bypass required controls.

### Scenario E: Dependency risk

**Hypothesis:** Production builds should use approved dependency sources
and reviewed dependency changes.

**Potential data sources:** lockfiles, dependency scanner output,
package registry logs, software bill of materials (SBOM), build
provenance.

**Detection logic:** 1. Detect new or changed dependencies. 2. Check
registry, version, vulnerability status, provenance, and policy. 3.
Review whether the change was approved and expected. 4. Block or
quarantine only according to a tested policy; otherwise raise a
prioritized alert.

------------------------------------------------------------------------

## 11. Incident Triage and Response Workflow

When an alert fires, follow a repeatable process.

### 11.1 Triage checklist

1.  **Validate the event:** Confirm the log source, timestamp, identity,
    asset, and relevant event details.
2.  **Establish context:** Identify the repository, workflow, runner,
    artifact, environment, and business purpose.
3.  **Check authorization:** Compare the action with approved changes,
    release schedules, access policy, and expected job permissions.
4.  **Correlate evidence:** Review identity, code, build, network,
    dependency, and deployment events around the same time.
5.  **Assess severity:** Consider confidence, privileges, production
    impact, and potential blast radius.
6.  **Preserve evidence:** Retain relevant logs, hashes, artifact
    identifiers, diffs, and audit events according to policy.
7.  **Escalate appropriately:** Follow the incident-response plan and
    notify the responsible team.
8.  **Contain safely:** Depending on evidence and authority, disable a
    compromised token, pause a pipeline, isolate a runner, or stop a
    release.
9.  **Eradicate and recover:** Remove unauthorized changes, rebuild from
    trusted sources, rotate exposed credentials, patch weaknesses, and
    validate artifacts.
10. **Improve detection:** Document root cause, update controls and
    rules, and test that the original behavior is detected.

### 11.2 Evidence handling

-   Keep original logs where possible and record how evidence was
    collected.
-   Use synchronized timestamps and document time-zone assumptions.
-   Limit evidence access to authorized personnel.
-   Record actions taken during the investigation.
-   Follow the organization's retention, privacy, and legal
    requirements.
-   Avoid destroying evidence during containment unless immediate safety
    needs require it.

### 11.3 Severity is not the same as anomaly score

A statistical anomaly score measures how unusual an event appears under
a model. It does not necessarily measure attacker intent, business
impact, or confidence in compromise. Combine anomaly scores with asset
criticality, privilege level, known threat context, and corroborating
evidence.

------------------------------------------------------------------------

## 12. Practical Lab: Review CI/CD Events

This tabletop lab uses fictional data and requires no external systems.
It is designed to practice reasoning rather than prove that an attack
occurred.

### 12.1 Sample event table

  -----------------------------------------------------------------------
  Event ID          Time (UTC)        Event             Context
  ----------------- ----------------- ----------------- -----------------
  E-101             09:00             Build completed   Approved runner;
                                      successfully      normal release
                                                        branch

  E-102             09:12             Workflow file     Change has no
                                      modified          linked review
                                                        record

  E-103             09:14             Build runner      Job normally uses
                                      requests          test-only
                                      production secret credentials

  E-104             09:15             Runner connects   Destination not
                                      to unfamiliar     in the team's
                                      external domain   approved list

  E-105             09:18             Production        No matching
                                      deployment starts approval record
                                                        found

  E-106             09:20             Build duration    One run only;
                                      increases by 40%  possible benign
                                                        explanation
  -----------------------------------------------------------------------

### 12.2 Analysis questions

1.  Which event should be investigated first, and why?
2.  Which events may be related?
3.  What additional logs would help confirm or refute a compromise?
4.  Which observations are anomalies, possible IoAs, possible IoCs, or
    policy violations?
5.  What immediate actions may be justified while preserving evidence?
6.  What preventive and detective controls could reduce recurrence?

### 12.3 Suggested analysis

-   **Highest concern:** E-102 through E-105 form a suspicious sequence:
    an unreviewed workflow change, unexpected production-secret access,
    unfamiliar outbound communication, and an unapproved production
    deployment. Together they justify urgent investigation.
-   **Possible classifications:** E-102 is a suspicious configuration
    change and potential IoA; E-103 is a suspicious secret-access event
    and potential IoA; E-104 is an anomaly that needs destination and
    process context; E-105 is a policy violation and potential IoA. None
    should be called a confirmed compromise without supporting evidence.
-   **E-106:** A 40% duration increase is an anomaly, but may result
    from cache misses, infrastructure load, dependency changes, or
    malicious activity. Investigate in context rather than treating it
    as proof.
-   **Useful evidence:** Workflow diff, actor and authentication logs,
    approval records, secret-manager audit events, runner process
    telemetry, DNS/proxy/firewall records, artifact hashes, dependency
    changes, and deployment provenance.
-   **Possible containment:** Follow the incident plan. Depending on
    evidence and authority, pause the release, restrict the runner,
    revoke a potentially exposed token, and preserve logs and artifacts.
    Avoid destroying the runner before evidence is captured unless
    immediate containment requires it.
-   **Follow-up controls:** Protect workflow files, require reviews and
    production approvals, use short-lived least-privilege credentials,
    restrict outbound traffic, validate artifacts, and create tested
    SIEM correlations.

### 12.4 Lab deliverable

Write a short incident note with:

-   Summary of the suspected activity.
-   Timeline of relevant events.
-   Evidence available and evidence still needed.
-   Confidence level and rationale.
-   Potential impact and affected assets.
-   Immediate containment recommendation.
-   Follow-up detection and prevention improvements.

------------------------------------------------------------------------

## 13. Metrics and Evaluation

Use metrics to understand whether detection is useful, timely, and
maintainable.

  -----------------------------------------------------------------------
  Metric                  What it measures        Interpretation
  ----------------------- ----------------------- -----------------------
  Mean Time to Detect     Average time from       Lower can indicate
  (MTTD)                  relevant                faster detection;
                          event/compromise onset  reliable timestamps are
                          to detection, according essential
                          to a defined            
                          measurement method      

  Mean Time to            Average time from alert Helps assess alert
  Acknowledge (MTTA)      creation to analyst     routing and staffing
                          acknowledgement         

  Mean Time to            A term used for         Do not compare values
  Respond/Recover (MTTR)  response or recovery    unless definitions
                          time; define the exact  match
                          start and end points    

  True-positive rate /    Fraction of known       High recall is useful,
  recall                  malicious test cases    but does not describe
                          detected                false alarms alone

  Precision               Fraction of generated   Low precision can
                          alerts that are true    create alert fatigue
                          positives under the     
                          chosen labeling process 

  False-positive rate     Fraction of benign      Define the denominator
                          cases incorrectly       clearly; it is not the
                          flagged                 same as the proportion
                                                  of alerts that are
                                                  false

  Detection coverage      Portion of defined      Reveals blind spots
                          critical behaviors or   
                          assets with tested      
                          detections              

  Alert-to-incident ratio Relationship between    Useful operational
                          alerts and incidents    context, but not a
                          after investigation     quality score by itself

  Rule health             Whether detections run, Prevents silent
                          parse data, and pass    detection failures
                          expected test cases     

  Logging completeness    Whether required event  Missing telemetry
                          sources and fields are  weakens all downstream
                          available               detection
  -----------------------------------------------------------------------

### 13.1 Basic formulas

Let:

-   (TP) = true positives
-   (FP) = false positives
-   (FN) = false negatives
-   (TN) = true negatives

\[ `\text{Precision}`{=tex} = `\frac{TP}{TP+FP}`{=tex} \]

\[ `\text{Recall}`{=tex} = `\frac{TP}{TP+FN}`{=tex} \]

\[ `\text{False Positive Rate}`{=tex} = `\frac{FP}{FP+TN}`{=tex} \]

Precision answers: "Of the alerts labeled positive, how many were truly
positive?" Recall answers: "Of the known positive cases, how many did
the system detect?" These metrics require a defined and trustworthy
labeling process.

------------------------------------------------------------------------

## 14. Common Mistakes and Technical Clarifications

1.  **Treating every alert as a confirmed incident.** An alert
    identifies activity for review; validate it with context and
    evidence.
2.  **Treating no alerts as proof of safety.** A system may be
    unmonitored, misconfigured, or blind to the behavior.
3.  **Treating every anomaly as malicious.** Benign operational changes
    can be unusual.
4.  **Treating a CVE finding as an IoC.** A CVE usually describes a
    known vulnerability. It is a risk finding, not proof that an
    attacker exploited it.
5.  **Treating every threat-intelligence match as conclusive.** Check
    confidence, freshness, source, context, and potential shared
    infrastructure.
6.  **Relying on file hashes alone.** Hashes are useful for known
    artifacts, but do not detect every fileless, modified, or
    behavior-based attack.
7.  **Collecting logs without a response process.** Define alert
    ownership, severity, escalation, and evidence-handling procedures.
8.  **Learning baselines without governance.** If new behavior is
    automatically accepted as normal, attacker activity may poison the
    baseline.
9.  **Logging secrets for debugging.** Monitoring should avoid
    collecting secret values; use masking, restricted access, and
    secret-store audit events.
10. **Ignoring the software supply chain.** Source code is only one part
    of the pipeline; runners, actions/plugins, dependencies, artifacts,
    signing keys, and deployment credentials also matter.
11. **Overblocking from weak indicators.** Poorly validated indicators
    can disrupt legitimate services. Use staged response and risk-based
    enforcement.
12. **Confusing delivery with deployment.** Continuous delivery commonly
    retains a release decision; continuous deployment automatically
    deploys changes that pass the required gates.

------------------------------------------------------------------------

## 15. Implementation Checklist

### Detection foundations

-   [ ] Identify critical assets, repositories, build runners, artifact
    stores, and deployment environments.
-   [ ] Define which events must be logged and who owns each source.
-   [ ] Synchronize clocks and document timestamp conventions.
-   [ ] Centralize logs where practical and restrict access.
-   [ ] Test log completeness, retention, and alert delivery.

### Source control and identity

-   [ ] Protect important branches and pipeline configuration files.
-   [ ] Require review and appropriate approvals for sensitive changes.
-   [ ] Monitor permission changes, token creation, MFA events, and
    unusual authentication.
-   [ ] Use individual identities or tightly governed service identities
    rather than shared accounts where possible.
-   [ ] Review and remove unnecessary permissions.

### Build and dependency security

-   [ ] Use trusted, maintained runners and restrict privileged
    execution.
-   [ ] Pin or otherwise control dependencies and external pipeline
    actions according to policy.
-   [ ] Scan dependencies, container images, and infrastructure
    configuration.
-   [ ] Record artifact identity and provenance.
-   [ ] Restrict build egress to necessary destinations.
-   [ ] Monitor changes to build steps and security gates.

### Secrets and deployment

-   [ ] Store secrets in an approved secrets manager.
-   [ ] Use least privilege and short-lived credentials where supported.
-   [ ] Prevent secrets from being printed into logs.
-   [ ] Run secret scanning on code and relevant artifacts.
-   [ ] Require approvals and policy checks for production deployments.
-   [ ] Monitor access to signing keys and deployment credentials.

### Detection and response

-   [ ] Create and test rules for high-impact behaviors.
-   [ ] Correlate identity, source-control, build, network, and
    deployment events.
-   [ ] Enrich alerts with asset, actor, job, and threat context.
-   [ ] Define severity, escalation, and response owners.
-   [ ] Conduct tabletop exercises and validate detections with safe
    simulations.
-   [ ] Review false positives, missed detections, and rule health.
-   [ ] Convert confirmed findings into improved controls and detection
    logic.

------------------------------------------------------------------------

## 16. Glossary

  -----------------------------------------------------------------------
  Term                                Definition
  ----------------------------------- -----------------------------------
  APT                                 Advanced Persistent Threat; a
                                      capable, persistent actor or
                                      campaign pursuing objectives over
                                      time

  CI/CD                               Continuous Integration and
                                      Continuous Delivery/Deployment

  CVE                                 Common Vulnerabilities and
                                      Exposures identifier for a publicly
                                      catalogued vulnerability

  C2                                  Command and Control infrastructure
                                      used by an attacker to communicate
                                      with compromised systems

  Detection                           Identification of a potentially
                                      security-relevant event or behavior

  False positive                      A benign event incorrectly
                                      classified as positive/malicious

  Honeypot                            Decoy system or service designed to
                                      attract and observe unauthorized
                                      interaction

  IDS                                 Intrusion Detection System

  IoA                                 Indicator of Attack; behavior or a
                                      sequence suggesting an attack may
                                      be underway

  IoC                                 Indicator of Compromise; observable
                                      artifact or evidence associated
                                      with possible compromise

  MTTD                                Mean Time to Detect

  MTTA                                Mean Time to Acknowledge

  MTTR                                Mean Time to Respond or Recover;
                                      the exact definition should be
                                      stated

  SIEM                                Security Information and Event
                                      Management

  TIP                                 Threat Intelligence Platform

  Threat hunting                      Proactive search for threats that
                                      automated detections may have
                                      missed

  Threat intelligence                 Evidence-based information about
                                      threats enriched with context for
                                      defensive decisions

  TTP                                 Tactics, Techniques, and Procedures
                                      used by threat actors

  SBOM                                Software Bill of Materials; a
                                      record of software components and
                                      dependencies
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 17. Knowledge Check

### Questions

1.  What is the difference between detection and analysis?
2.  Why should a SIEM not be treated as a complete security solution by
    itself?
3.  How does threat hunting differ from waiting for automated alerts?
4.  Why should a threat-intelligence feed be assessed before a block
    rule is applied?
5.  What is the difference between an IoC, an IoA, an anomaly, and a
    vulnerability finding?
6.  Why is an unexpected production-secret request from a test job
    suspicious?
7.  Name four log sources that can help investigate a compromised CI/CD
    pipeline.
8.  Why can a longer-than-usual build be a useful signal but not proof
    of an attack?
9.  What should a team do when a secret is discovered in a public or
    broadly accessible repository?
10. Give two controls that can reduce the risk of unauthorized
    production deployment.

### Answer key

1.  Detection identifies a potentially relevant event; analysis
    investigates and validates its meaning and impact.
2.  A SIEM depends on telemetry quality, configuration, detection logic,
    context, and a response process.
3.  Threat hunting proactively tests hypotheses and searches data for
    hidden or missed activity.
4.  Indicators can be stale, low-confidence, shared by legitimate
    services, or irrelevant to the organization's environment.
5.  An IoC is an artifact/evidence associated with compromise; an IoA is
    attack-related behavior; an anomaly is a deviation from baseline; a
    vulnerability finding identifies a weakness.
6.  The job may be requesting a credential beyond its intended scope,
    which could indicate misconfiguration or an attempt to obtain
    privileged access.
7.  Examples: source-control audit logs, identity logs, pipeline/build
    logs, secret-manager logs, network/DNS logs, deployment logs, and
    endpoint telemetry.
8.  Benign factors such as load, cache misses, or dependency delays can
    increase duration; corroborating evidence is needed.
9.  Treat the secret as exposed, revoke/rotate it, investigate possible
    use, remove exposure safely, and improve preventive controls.
10. Examples: mandatory approvals, protected branches, least-privilege
    deployment identities, policy checks, and artifact provenance
    verification.

------------------------------------------------------------------------

## 18. References and Further Learning

The following resources are useful starting points. Review each source's
current content and applicability before using it as an operational
standard.

### General threat hunting and threat intelligence

-   [The ThreatHunting Project](https://www.threathunting.net/) ---
    Community resources and material related to threat hunting.
-   [Google Threat Analysis Group
    (TAG)](https://blog.google/threat-analysis-group/) --- Research and
    reporting on threats, including activity attributed to
    state-sponsored actors.
-   [MITRE ATT&CK](https://attack.mitre.org/) --- Knowledge base of
    adversary tactics and techniques; useful for threat hunting
    hypotheses and detection mapping.
-   [CISA Cybersecurity
    Advisories](https://www.cisa.gov/news-events/cybersecurity-advisories)
    --- Government advisories and defensive recommendations.
-   [NIST Computer Security Incident Handling Guide (SP
    800-61)](https://csrc.nist.gov/pubs/sp/800/61/r2/final) ---
    Incident-handling guidance. Check NIST for newer related
    publications and revisions.

### CI/CD and software supply-chain monitoring

-   [OWASP Software Supply Chain
    Security](https://owasp.org/www-project-software-supply-chain-security/)
    --- Guidance and resources on software supply-chain risks.
-   [SLSA (Supply-chain Levels for Software
    Artifacts)](https://slsa.dev/) --- Framework and guidance for
    improving software artifact integrity and provenance.
-   [OpenSSF](https://openssf.org/) --- Open-source software security
    projects and resources.
-   [Coralogix: Optimizing logs for a more effective CI/CD
    pipeline](https://coralogix.com/blog/optimizing-logs-for-a-more-effective-ci-cd-pipeline/)
    --- Practical discussion of CI/CD logging.
-   [Splunk: CI/CD and DevOps
    pipelines](https://www.splunk.com/en_us/blog/learn/ci-cd-devops-pipeline.html)
    --- Introductory pipeline concepts and monitoring context.

### Note on supplied links

The source text included some URLs ending in `svg`, apparently attached
to the URL rather than clearly part of the intended page address. The
links above use the likely canonical page paths where identifiable. If a
page has moved, search the publisher's site for the title.

------------------------------------------------------------------------

## Final Summary

Effective cybersecurity detection requires more than a single tool. IDS
and SIEM technologies help identify and correlate suspicious events;
threat hunting proactively investigates what automated controls may
miss; threat intelligence adds context; and cyber deception can create
useful detection signals. In CI/CD environments, monitoring should cover
source control, identity, workflow configuration, runners, dependencies,
secrets, network egress, artifacts, and deployments.

The most reliable approach combines **good telemetry + layered
detection + contextual analysis + a tested response process + continuous
improvement**. Treat alerts as evidence to investigate, distinguish risk
findings from proof of compromise, and validate detection rules
regularly.
