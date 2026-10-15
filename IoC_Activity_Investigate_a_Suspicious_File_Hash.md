# Activity: Investigate a Suspicious File Hash

## 1. Activity Overview

In this activity, you will analyze a suspicious file using **VirusTotal** and document related **Indicators of Compromise (IoCs)** using the **Pyramid of Pain**.

Security analysts use IoCs as observable evidence that may indicate a security incident. The Pyramid of Pain explains the different types of IoCs and the relative difficulty attackers may face when defenders identify and block them.

VirusTotal is a threat-intelligence service that analyzes submitted or queried files, URLs, domains, and IP addresses. It aggregates results from security vendors and other sources to help analysts assess whether an artifact has been reported as malicious.

> **Privacy and safety warning:** Files uploaded to VirusTotal may be shared with the VirusTotal community and security partners. Do not upload confidential files, personal information, company data, or other sensitive material. Search for an existing hash before considering any upload. Follow your organization's evidence-handling and data-sharing policies.

---

## 2. Learning Objectives

By completing this activity, you should be able to:

- Search VirusTotal for a file using its SHA-256 hash.
- Interpret vendor detections and community reputation cautiously.
- Examine file metadata and additional hashes.
- Investigate related IP addresses, domains, URLs, files, and observed behavior.
- Identify at least three different types of IoCs.
- Map findings to the relevant levels of the Pyramid of Pain.
- Record evidence and explain a defensible assessment of whether a file is malicious.

---

## 3. Scenario

You are a Level 1 Security Operations Center (SOC) analyst at a financial services company. You receive an alert about a suspicious file downloaded to an employee's computer.

Your initial investigation finds that:

1. The employee received an email containing a password-protected spreadsheet attachment.
2. The email provided the password needed to open the spreadsheet.
3. The employee downloaded and opened the file.
4. After the file was opened, a malicious payload reportedly executed on the computer.
5. Multiple unauthorized executable files were created.
6. An intrusion detection system detected the executable files and alerted the SOC.

You retrieve the suspicious file and calculate its SHA-256 hash. You will use that hash to search VirusTotal for additional threat-intelligence information.

### Incident timeline

| Time | Event |
|---|---|
| 1:11 p.m. | Employee receives an email containing a file attachment. |
| 1:13 p.m. | Employee downloads and opens the file. |
| 1:15 p.m. | Multiple unauthorized executable files are created on the employee's computer. |
| 1:20 p.m. | An intrusion detection system detects the executable files and sends an alert to the SOC. |

**Important:** This timeline is provided by the scenario. Validate the details against actual endpoint, email, and security logs during a real investigation.

---

## 4. Artifact Under Investigation

**SHA-256 file hash:**

```text
54e6ea47eb04634d3e87fd7787e2136ccfbcc80ade34f246a12cf93bab527f6b
```

A cryptographic hash is a fixed-length value calculated from data. A file's hash can be used as a fingerprint to identify that exact file content. Even a small change to the file generally produces a different hash.

A hash is an identifier, not proof by itself that a file is malicious. Its reputation and surrounding evidence must be evaluated.

---

## 5. Tools and References

- **VirusTotal:** <https://www.virustotal.com/>
- **Pyramid of Pain:** Use the course-provided Pyramid of Pain template.
- **Incident Handler's Journal:** Use the journal started in the earlier course activity.
- **MITRE ATT&CK:** <https://attack.mitre.org/>

VirusTotal reports and vendor verdicts can change over time. Record the date you performed the lookup and the evidence shown in the report.

---

## 6. Investigation Procedure

### Step 1 — Open the Pyramid of Pain template

Open the course-provided **Pyramid of Pain** template and select **Use Template**, if that option is available. Alternatively, download the provided template.

Keep the template ready to record your findings.

### Step 2 — Search VirusTotal for the hash

1. Open <https://www.virustotal.com/>.
2. Select the search function.
3. Enter the SHA-256 hash exactly as provided above.
4. Submit the search.
5. Open the report that matches the hash.
6. Record the date of the lookup and the report details needed for your analysis.

**Do not assume that a current report will necessarily match an older course exemplar.** Vendor results, community scores, and related artifacts may change over time.

### Step 3 — Review the Detection tab

Examine the vendor detection summary and the list of security vendors' results.

Record:

- The vendor detection ratio shown in the report.
- The number of vendors identifying the file as malicious or suspicious, as displayed.
- Relevant detection names or malware-family labels, if present.
- The community score, if available.
- Whether the vendors' results broadly agree or are mixed.

Interpretation guidance:

- A high proportion of malicious detections supports a malicious assessment.
- A negative community score can support further investigation, but it is not definitive proof.
- Different vendors may use different labels or detection methods.
- A low detection count or no detections does **not** prove that a file is safe.
- Do not treat every vendor label as equally reliable or assume that all detections are independent.

Record the exact figures shown in your report; do not estimate them.

### Step 4 — Review the Details tab

The **Details** tab may provide static file metadata and additional identifiers.

Look for:

- MD5 hash.
- SHA-1 hash.
- SHA-256 hash.
- File type and file size.
- Other metadata relevant to the artifact.

For the Pyramid of Pain, identify a hash **other than the supplied SHA-256 hash** if the report provides one. Copy it exactly and label its hash type.

Do not confuse a file hash with a filename. Filenames can be changed; a cryptographic hash identifies file content.

### Step 5 — Review the Relations tab

The **Relations** tab can show related artifacts, including IP addresses, domain names, URLs, and other files.

Investigate:

- Contacted IP addresses.
- Contacted domains.
- Related URLs.
- Files dropped or otherwise associated with the artifact, where available.
- Detection information associated with each related item.

For each candidate indicator, record the exact value and the context in which it appears. Check the associated detection information before describing an IP address or domain as malicious.

**Caution:** A malware sample may contact legitimate infrastructure, shared hosting, content delivery networks, or compromised websites. A relationship alone does not prove that an IP address or domain is malicious.

### Step 6 — Review the Behavior tab

The **Behavior** tab may contain reports from sandboxed execution. A sandbox is an isolated environment used to observe what a file does when run.

Review the available sandbox reports and look for:

- Processes created or launched.
- Files created, modified, or dropped.
- Registry activity.
- Network traffic and contacted IP addresses or domains.
- Persistence-related behavior.
- Security-disabling or defense-evasion behavior.
- MITRE ATT&CK tactics and techniques, if reported.

If multiple sandbox reports are available, compare them. Prefer behaviors supported by more than one report or corroborated by other evidence. Some reports may contain irrelevant, environment-specific, or potentially misleading observations.

Do not execute a suspicious file on your normal workstation to reproduce sandbox behavior.

### Step 7 — Decide whether the file is malicious

Use the evidence together rather than relying on one indicator.

Consider:

1. Vendor detection ratio and verdicts.
2. Community score, if available.
3. Detection names and consistency between vendors.
4. File metadata and related hashes.
5. Network relationships and their detection information.
6. Sandbox behavior and any reported ATT&CK techniques.
7. The scenario's suspicious delivery and execution timeline.

Write a conclusion that clearly distinguishes observed evidence from assumptions. If the report does not provide enough evidence for a confident decision, record the result as **inconclusive pending further investigation** rather than claiming certainty.

### Step 8 — Select three different types of IoCs

The activity asks you to identify **three different types** of IoCs associated with the file. Choose indicators supported by the report.

Possible categories include:

| IoC category | Where to look | What to record |
|---|---|---|
| Hash value | Details | An additional hash, such as MD5 or SHA-1, if available. |
| IP address | Relations or Behavior | An IP address associated with observed network communication. |
| Domain name | Relations | A domain associated with the artifact; check its detection status and context. |
| Network or host artifact | Behavior or Relations | A specific file, path, registry change, process, or network artifact reported by the analysis. |
| Tool | Behavior | A tool identified in the observed activity, if supported by the report. |
| Tactics, techniques, and procedures (TTPs) | Behavior | Reported MITRE ATT&CK tactics or techniques supported by the sandbox evidence. |

Choose any three distinct categories for which you have evidence. Do not invent values to fill every category. If a category is absent from the report, note that it was not observed or was not available.

### Step 9 — Complete the Pyramid of Pain template

Enter the selected IoCs into their corresponding categories in the course template.

The Pyramid of Pain is commonly presented from lower to higher attacker pain in this order:

1. **Hash values** — usually easiest for an attacker to change by modifying a file.
2. **IP addresses** — attackers may change infrastructure or use alternate addresses.
3. **Domain names** — attackers may register or switch domains, although doing so can require more effort.
4. **Network/host artifacts** — behavioral or system artifacts can be more difficult to change without affecting functionality.
5. **Tools** — replacing or modifying tools can require additional effort.
6. **Tactics, techniques, and procedures (TTPs)** — changing established methods and operational behavior can be especially difficult.

The exact impact depends on the threat actor and context. Use the labels and layout in your course-provided template if they differ in presentation.

### Step 10 — Document your findings

Record the findings in your incident handler's journal. Include the hash searched, date of analysis, the evidence supporting your conclusion, three distinct IoCs, and any limitations or follow-up actions.

Do not include confidential company information in public threat-intelligence services or public reports.

---

## 7. Investigation Worksheet

Complete this worksheet using the actual VirusTotal report.

### A. Lookup details

| Field | Finding |
|---|---|
| Artifact type | File |
| Hash type searched | SHA-256 |
| SHA-256 | `54e6ea47eb04634d3e87fd7787e2136ccfbcc80ade34f246a12cf93bab527f6b` |
| Date of lookup | `[Enter date]` |
| VirusTotal report URL or report identifier | `[Enter if available]` |
| File name / file type | `[Enter report finding, if available]` |

### B. Detection assessment

| Field | Finding |
|---|---|
| Vendor detection ratio | `[Enter exact ratio shown]` |
| Community score | `[Enter score if available]` |
| Examples of vendor verdicts | `[Record relevant vendor names and verdicts]` |
| Detection names or malware-family labels | `[Enter if reported]` |
| Overall assessment | `[Malicious / Suspicious / Inconclusive / Other, with justification]` |

### C. Three distinct IoCs

| No. | IoC category | Exact indicator | VirusTotal section | Evidence / context |
|---:|---|---|---|---|
| 1 | `[e.g., Hash value]` | `[Paste exact value]` | `[Details / Relations / Behavior]` | `[Explain why it is relevant]` |
| 2 | `[e.g., IP address]` | `[Paste exact value]` | `[Relations / Behavior]` | `[Record observed relationship and detection context]` |
| 3 | `[e.g., Domain name]` | `[Paste exact value]` | `[Relations]` | `[Record detection status and context]` |

Use three **different categories**, not merely three indicators of the same category.

### D. Pyramid of Pain notes

| Pyramid category | Finding or notes |
|---|---|
| Hash values | `[Additional hash, if found]` |
| IP addresses | `[Related IP address, if found]` |
| Domain names | `[Related domain, if found]` |
| Network/host artifacts | `[Observed artifact, if found]` |
| Tools | `[Tool supported by evidence, if found]` |
| TTPs | `[Reported ATT&CK tactics/techniques, if found]` |

You only need to enter the three distinct IoC types required by the activity. Other categories can be left blank or marked **Not observed / Not available** if the report does not support a finding.

---

## 8. Suggested Incident Handler's Journal Entry

Use the following structure to document the investigation. Replace all bracketed placeholders with your actual findings.

### Incident: Suspicious password-protected spreadsheet

**Date/time of analysis:** `[Enter date and time]`  
**Analyst:** `[Enter analyst identifier according to your organization's policy]`  
**Artifact SHA-256:** `54e6ea47eb04634d3e87fd7787e2136ccfbcc80ade34f246a12cf93bab527f6b`

**Summary**

An employee received a password-protected spreadsheet by email, downloaded and opened it, and subsequently had multiple unauthorized executable files created on the computer. An intrusion detection system alerted the SOC. The supplied SHA-256 hash was searched in VirusTotal to investigate the file's reputation and related IoCs.

**Evidence reviewed**

- Detection tab: `[Summarize vendor ratio, verdicts, and community score]`
- Details tab: `[Summarize file metadata and additional hashes]`
- Relations tab: `[Summarize relevant IP addresses, domains, URLs, and associated detection context]`
- Behavior tab: `[Summarize relevant observed activity and ATT&CK mappings, if available]`

**Assessment**

`[State whether the file is malicious, suspicious, or inconclusive. Cite the specific report findings that support the assessment.]`

**Indicators of compromise**

1. `[Category and exact indicator]` — `[Evidence/context]`
2. `[Category and exact indicator]` — `[Evidence/context]`
3. `[Category and exact indicator]` — `[Evidence/context]`

**Limitations**

`[Record missing report sections, conflicting detections, unavailable sandbox data, or other limitations.]`

**Recommended follow-up**

- Preserve the suspicious file and related logs according to evidence-handling policy.
- If compromise is credible, follow the organization's incident-response procedure to contain the affected endpoint.
- Investigate related processes, dropped files, persistence, network connections, and other affected systems.
- Search authorized internal telemetry for the identified IoCs.
- Block or monitor indicators only after validating their relevance and assessing the risk of false positives.
- Escalate to the appropriate incident-response personnel according to organizational procedures.

---

## 9. How to Interpret the Pyramid of Pain

The Pyramid of Pain connects the types of indicators defenders use with the effort an attacker may need to expend when those indicators are detected and blocked.

| Category | Typical defender use | Relative attacker pain |
|---|---|---|
| Hash values | Identify a specific file by its content hash. | Low |
| IP addresses | Detect or block communication with known infrastructure. | Low to moderate |
| Domain names | Detect or block domains associated with activity. | Moderate |
| Network/host artifacts | Detect distinctive artifacts left in network traffic or on a host. | Moderate to high |
| Tools | Identify or disrupt tools used by the attacker. | High |
| TTPs | Detect the attacker's methods and patterns of behavior. | Highest in the conventional model |

A file hash can be precise for a known sample, but a modified sample may have a different hash. Behavioral detections and TTP-based detections can remain useful across variations, although they also require careful tuning and validation.

---

## 10. Common Mistakes to Avoid

- **Treating a hash as proof of maliciousness:** The hash identifies file content; the associated evidence supports the verdict.
- **Assuming no detections means safe:** New, uncommon, packed, or targeted malware may not be detected.
- **Calling every related domain malicious:** Check the detections and context for each related artifact.
- **Copying an indicator inaccurately:** Preserve the exact hash, IP address, domain, path, or technique identifier.
- **Recording three indicators from only one category:** The activity requests three different IoC types.
- **Inventing missing findings:** If the report does not contain an indicator, mark it unavailable rather than guessing.
- **Ignoring the report date:** Threat-intelligence results can change.
- **Uploading sensitive files:** Search by hash first and follow organizational data-handling policy.
- **Running malware on a normal computer:** Use approved, isolated analysis environments.

---

## 11. Validation Checklist

Before submitting the activity, confirm that you have:

- [ ] Searched VirusTotal using the exact SHA-256 hash.
- [ ] Recorded the lookup date.
- [ ] Reviewed the Detection tab and documented the available evidence.
- [ ] Reviewed the Details tab for file metadata and additional hashes.
- [ ] Reviewed the Relations tab for relevant related artifacts.
- [ ] Reviewed the Behavior tab and sandbox findings, where available.
- [ ] Written a reasoned assessment of the file's maliciousness.
- [ ] Identified three IoCs from three different categories.
- [ ] Entered the selected IoCs into the appropriate Pyramid of Pain sections.
- [ ] Documented findings and limitations in the incident handler's journal.
- [ ] Avoided unsupported claims and protected sensitive information.

---

## 12. Submission Requirements

The completed course template must contain:

1. **A statement about whether the file hash is malicious**, supported by the actual VirusTotal evidence.
2. **Three different types of IoCs** associated with the file, copied accurately from the report and entered into their corresponding Pyramid of Pain categories.

The source activity supplies the SHA-256 hash and incident timeline, but it does not include the current VirusTotal detection ratio, community score, related IP addresses, domains, or sandbox findings. Obtain those details from the report during your investigation and record them in the worksheet above.

---

## 13. Key Takeaways

- VirusTotal can help analysts assess an artifact using aggregated security-vendor and community intelligence.
- The Detection, Details, Relations, and Behavior tabs provide complementary evidence.
- IoCs help identify and investigate suspicious activity, but each indicator must be interpreted in context.
- The Pyramid of Pain explains why some indicators are easier for attackers to change than others.
- Good SOC documentation distinguishes confirmed observations, interpretations, and unknowns.

**End of activity.**
