# Incident Handler’s Journal

**Cybersecurity Portfolio — Incident Detection and Response**

## Entry #1 — Documenting a Ransomware Incident

**Date:** July 23, 2024  
**Entry:** #1

### Description

This entry documents a ransomware incident affecting a healthcare company. The investigation involved identifying the initial attack, analyzing how the attackers gained access, and reviewing the organization's response. The incident relates to the Detection and Analysis and Containment, Eradication, and Recovery phases of the NIST Incident Response Lifecycle. The organization shut down its computer systems and contacted external organizations for technical assistance.

### Tool(s) used

None. This activity focused on documenting an incident scenario and understanding the incident response process rather than operating a cybersecurity tool.

### The 5 W’s

- **Who:** An organized group of malicious actors carried out the ransomware attack. The specific identities of the attackers were not established in the scenario.
- **What:** A ransomware incident encrypted critical files and disrupted the healthcare company's computer systems. The attackers left a ransom note demanding payment in exchange for a decryption key.
- **When:** The incident was detected on Tuesday at approximately 9:00 a.m., according to the scenario.
- **Where:** The incident affected the computer systems of a healthcare company.
- **Why:** The attackers reportedly gained access through a phishing attack. Their apparent motivation was financial gain, as indicated by the ransom demand.

### Additional notes

1. **How could the company prevent similar incidents?** The organization should implement phishing awareness training, multifactor authentication, email filtering, endpoint protection, network segmentation, regular patching, and tested offline backups.
2. **Should the company pay the ransom?** Payment should not be the default response. The organization should consult incident response specialists, legal counsel, relevant authorities, and its recovery team before making a decision. Payment does not guarantee successful data recovery.

The incident highlights the importance of preparation, timely detection, containment, reliable backups, and a documented incident response plan.

---

## Entry #2 — Analyzing a Packet Capture with Wireshark

**Date:** July 25, 2024  
**Entry:** #2

### Description

This entry documents my first experience analyzing a packet capture file using Wireshark. The activity introduced me to network protocol analysis and showed how packet-level information can help identify suspicious communications, understand network behavior, and investigate security incidents. It primarily relates to the Detection and Analysis phase of the NIST Incident Response Lifecycle because network traffic can provide evidence of potential threats.

### Tool(s) used

**Wireshark** is a graphical network protocol analyzer that enables analysts to inspect captured network traffic.

- I used Wireshark to open and examine a packet capture file.
- I explored packet details and the information displayed in the interface.
- I learned that protocol filters can help narrow an investigation to relevant traffic.
- The tool can support investigations into suspicious connections, unusual protocols, and potential indicators of compromise.

### The 5 W’s

Not applicable to this activity because it focused on learning a cybersecurity tool rather than investigating a specific confirmed incident.

### Additional notes

I had never used Wireshark before this activity, and its interface initially seemed overwhelming. However, exploring the packet list and packet details helped me understand how network communications can be examined. I learned that effective analysis requires patience, familiarity with network protocols, and the ability to distinguish normal traffic from potentially suspicious behavior.

---

## Entry #3 — Capturing Network Traffic with tcpdump

**Date:** July 25, 2024  
**Entry:** #3

### Description

This entry records my first experience capturing network traffic using tcpdump, a command-line network packet analyzer. The activity helped me understand how network packets can be captured, filtered, and saved for further investigation. It relates to the Detection and Analysis phase of the NIST Incident Response Lifecycle because packet captures can help analysts investigate suspicious communications and collect technical evidence.

### Tool(s) used

**tcpdump** is a command-line utility used to capture and inspect network packets.

- I used tcpdump to capture network traffic from an available network interface.
- I practiced using command syntax and packet-capture options.
- I learned that filters can restrict captured traffic to relevant hosts, ports, or protocols.
- I discovered that incorrect commands or interface selections can prevent the expected output, making careful validation important.

### The 5 W’s

Not applicable to this activity because the exercise focused on learning packet capture rather than investigating a specific confirmed incident.

### Additional notes

Using the command line was challenging because I was unfamiliar with tcpdump syntax and initially entered incorrect commands. By reviewing the instructions, correcting my mistakes, and repeating the necessary steps, I was able to progress through the activity. This experience taught me to validate commands carefully, examine their output, and troubleshoot errors systematically.

---

## Entry #4 — Investigating a Suspicious File Hash with VirusTotal

**Date:** July 27, 2024  
**Entry:** #4

### Description

This entry documents an investigation into a suspicious file hash reported by a security alert at a financial services company. I used VirusTotal to examine the hash and review available threat intelligence. The investigation relates to the Detection and Analysis phase of the NIST Incident Response Lifecycle because the objective was to assess a potential threat, examine evidence, and determine whether further incident response actions were necessary.

### Tool(s) used

**VirusTotal** is an online threat intelligence service that aggregates information from multiple security engines and other sources to help investigate suspicious files, URLs, domains, and IP addresses.

- I investigated the SHA-256 hash associated with the suspicious file.
- The hash provided in the activity was `54e6ea47eb04634d3e87fd7787e2136ccfbcc80ade34f246a12cf93bab527f6b`.
- I reviewed the available detection information to assess whether the file was associated with malicious activity.
- I learned that hash-based investigation can help analysts assess suspicious files without executing them.

A VirusTotal result should be treated as one source of evidence, not definitive proof by itself. Analysts should correlate the findings with endpoint alerts, email records, file metadata, and other available evidence.

### The 5 W’s

- **Who:** An unknown malicious actor was associated with the suspected malicious email attachment. The actor's identity was not established.
- **What:** An employee received an email containing a suspicious file attachment associated with the SHA-256 hash recorded above.
- **When:** The organization's security operations center received an alert at approximately 1:20 p.m., according to the activity scenario.
- **Where:** The incident involved an employee's computer at a financial services company.
- **Why:** The employee was able to download and execute the attachment, creating a potential opportunity for malicious activity. The attacker's precise motivation and the full impact were not established by the hash alone.

### Additional notes

To reduce the risk of similar incidents, the organization should strengthen email filtering, endpoint detection, attachment analysis, and employee security awareness training. Suspicious files should be isolated and investigated according to established procedures. Analysts should preserve relevant evidence, determine whether execution occurred, assess the scope of potential compromise, and escalate confirmed threats through the organization's incident response process.

---

# Reflections/Notes

## 1. Were there any specific activities that were challenging for you? Why or why not?

The tcpdump activity was the most challenging because I was unfamiliar with command-line syntax and initially struggled to obtain the expected output. By reviewing the instructions and correcting my commands, I completed the exercise and learned the importance of patience, accurate syntax, and systematic troubleshooting when using cybersecurity tools.

## 2. Has your understanding of incident detection and response changed since taking this course?

My understanding of incident detection and response has improved significantly throughout this course. I now better understand the incident response lifecycle, the importance of coordinated teams and documented procedures, and how security tools help analysts collect evidence, investigate alerts, contain threats, and support recovery.

## 3. Was there a specific tool or concept that you enjoyed the most? Why?

I most enjoyed learning about network traffic analysis using Wireshark and tcpdump because these tools helped me explore how devices communicate across networks. I found packet capture and protocol inspection particularly interesting, and I want to develop these skills further to investigate suspicious network behavior and support cybersecurity incident response.

---

## Portfolio Quality Checklist

- [x] Four dated and numbered journal entries are included.
- [x] Each entry contains a description, tools section, 5 W’s section, and additional notes.
- [x] Entries #1 and #4 document incident investigations using the 5 W’s.
- [x] Entries #2 and #3 document cybersecurity tool usage.
- [x] Relevant NIST Incident Response Lifecycle phases are identified.
- [x] All three reflection questions are answered.
- [x] Grammar, spelling, and professional presentation have been revised.

**Portfolio note:** This journal documents guided course activities and learning experiences. When sharing it with employers, distinguish simulated scenarios and educational exercises from real-world incident response experience. Review the original course instructions and scenario details before submission to ensure that dates, times, and incident facts match the supplied materials.
