# Exemplar Lab: Examine Alerts, Logs, and Rules with Suricata

> **Level:** Introductory  
> **Estimated duration:** 1 hour  
> **Cost:** No cost  
> **Focus:** Suricata rule structure, alert generation, and log analysis  
> **Lab environment:** Bash shell with `sample.pcap` and `custom.rules` supplied in `/home/analyst`

## 1. Learning objectives

By the end of this lab, you should be able to:

- Explain the purpose of Suricata and how rules/signatures match network traffic.
- Identify the **action**, **header**, and **rule options** in a Suricata rule.
- Run Suricata against a packet-capture file using a custom rule set.
- Read quick alert summaries in `fast.log`.
- Format and extract event fields from `eve.json` with `jq`.
- Use `flow_id` to correlate events belonging to the same network flow.
- Validate whether a rule behaves as expected by examining its generated alerts.

## 2. Activity overview and scenario

You are acting as a security analyst monitoring an organization's network. Your task is to inspect a custom Suricata rule, run it against a supplied packet capture, and investigate the logs produced.

Suricata is an open-source **intrusion detection system (IDS)**, **intrusion prevention system (IPS)**, and network analysis tool. It monitors traffic on network interfaces or processes packet-capture files, then evaluates packets against configured rules. Depending on the rule action and operating mode, traffic may be alerted on, passed, dropped, or rejected.

This lab uses two supplied files:

| File | Purpose |
|---|---|
| `sample.pcap` | A packet capture containing example network traffic. It provides repeatable input for rule testing. |
| `custom.rules` | A file containing custom Suricata rules. You inspect and modify it during the exercise. |

The lab produces logs in `/var/log/suricata/`:

| Log | Purpose |
|---|---|
| `fast.log` | A concise, human-readable alert summary. Useful for quick checks and basic rule-quality checks. The source material describes this format as deprecated and not recommended for incident response or threat hunting. |
| `eve.json` | Suricata's main standard event log in JSON format. It can contain detailed alert data and other network telemetry, making it more useful for structured analysis and downstream processing. |

> **Important:** The lab uses the terms *rule* and *signature* interchangeably. The supplied packet capture lets you repeat tests without generating live network traffic.

## 3. Before you begin

1. Start the lab using the course interface's **Start Lab** button.
2. Confirm that the provided Bash shell is available. The lab starts with the `analyst` user already logged in.
3. Work with the supplied `sample.pcap` and `custom.rules` files in the home directory.
4. When finished, use **End Lab** and confirm the prompt. Ending the lab removes access to the shell and its working environment.

The lab source recommends Google Chrome or Mozilla Firefox for compatibility with the course environment.

---

## 4. Task 1 — Examine a custom Suricata rule

### 4.1 Display the rule

Run:

```bash
cat custom.rules
```

The example rule supplied by the lab is:

```suricata
alert http $HOME_NET any -> $EXTERNAL_NET any (msg:"GET on wire"; flow:established,to_server; content:"GET"; http_method; sid:12345; rev:3;)
```

A Suricata signature has three main parts:

1. **Action** — what Suricata should do when the rule matches.
2. **Header** — the protocol, source/destination addresses and ports, and traffic direction.
3. **Rule options** — additional matching conditions and rule metadata, usually enclosed in parentheses and separated by semicolons.

### 4.2 Understand the action

The action appears first in the rule. Common actions described in the lab are:

| Action | Meaning in the lab context |
|---|---|
| `alert` | Generate an alert when the traffic matches. |
| `drop` | Generate an alert and drop matching traffic when Suricata is operating in IPS mode. |
| `pass` | Allow matching traffic to pass; a pass rule can override other rules in the relevant rule-processing context. |
| `reject` | Prevent matching traffic from passing and send a TCP reset packet in the described case, telling the communicating systems to stop the connection. |

The example begins with `alert`, so its purpose is to report matching traffic rather than block it.

The lab also illustrates a `pass` rule:

```suricata
pass http 172.17.0.77 any -> $EXTERNAL_NET any (msg:"BAD USER-AGENT"; flow:established,to_server; content:!"Mozilla/5.0"; http_user_agent; sid:12365; rev:1;)
```

This example uses a negated content match (`content:!`) in the user-agent inspection context. It is included to illustrate rule syntax; do not assume the example is a production-ready policy without validating its intended behavior and environment.

**Rule evaluation note:** The source material says that rules are loaded in configuration-file order, while Suricata's default action-processing order is `pass`, `drop`, `reject`, then `alert`. Rule-processing order can affect the final packet verdict. Do not assume that simply placing a rule earlier in a file always makes it take precedence.

### 4.3 Understand the header

The header in the example is:

```text
http $HOME_NET any -> $EXTERNAL_NET any
```

| Element | Meaning |
|---|---|
| `http` | The protocol/application-protocol selector used by this rule. |
| `$HOME_NET` | A variable representing the configured home or local network. |
| First `any` | Any source port. |
| `->` | Traffic direction: from the source side to the destination side. |
| `$EXTERNAL_NET` | A variable representing the configured external network. |
| Final `any` | Any destination port. |

In this lab, `$HOME_NET` is configured as `172.21.224.0/20` in `/etc/suricata/suricata.yaml`. The `$` prefix denotes a variable. Network variables and their definitions depend on the local Suricata configuration, so check the configuration before reusing a rule in another environment.

In plain language, the rule is intended to inspect HTTP traffic from the configured home network toward the configured external network.

### 4.4 Understand the rule options

The options in the example are:

```text
msg:"GET on wire";
flow:established,to_server;
content:"GET";
http_method;
sid:12345;
rev:3;
```

| Option | Purpose |
|---|---|
| `msg:"GET on wire"` | Text displayed to identify why the alert was generated. |
| `flow:established,to_server` | Restricts matching to an established flow in the client-to-server direction. |
| `content:"GET"` | Specifies the text to match. |
| `http_method` | Applies the content match to the HTTP method field, rather than treating `GET` as arbitrary payload text. |
| `sid:12345` | Signature ID used to identify the rule. |
| `rev:3` | Rule revision number; the example is revision 3. |

The options are enclosed in parentheses and separated by semicolons. The trailing semicolon before the closing parenthesis is part of the supplied example's syntax.

**Interpretation:** The rule is intended to alert when Suricata observes the HTTP method `GET` in traffic flowing from `$HOME_NET` to `$EXTERNAL_NET` on an established client-to-server flow.

---

## 5. Task 2 — Trigger the custom rule

### 5.1 Check the log directory

Run:

```bash
ls -l /var/log/suricata
```

The lab notes that the directory has no generated log files before Suricata is run for this exercise.

### 5.2 Run Suricata against the packet capture

Run:

```bash
sudo suricata -r sample.pcap -S custom.rules -k none
```

The command processes the saved capture with the specified rule file. Its options mean:

| Option | Meaning |
|---|---|
| `sudo` | Runs the command with elevated privileges, as required by this lab environment. Other environments may have different permission requirements. |
| `-r sample.pcap` | Reads packets from the supplied PCAP file instead of capturing live traffic. |
| `-S custom.rules` | Loads the specified rule file. |
| `-k none` | Disables packet checksum validation for this run. The lab uses a supplied capture, so checksum validation is disabled for the exercise. |

Suricata reports processing information in the terminal. When a packet matches an alert-generating rule, an alert entry is added to `fast.log`.

> **Operational caution:** Disabling checksum checks is a lab-specific choice, not a general recommendation for production monitoring. Use settings appropriate to the capture source and the task.

### 5.3 Confirm that logs were created

Run:

```bash
ls -l /var/log/suricata
```

The lab states that after Suricata runs, the directory contains several files, including `fast.log` and `eve.json`.

### 5.4 Read `fast.log`

Run:

```bash
cat /var/log/suricata/fast.log
```

The lab's example output contains two alerts:

```text
11/23/2022-12:38:34.624866  [**] [1:12345:3] GET on wire [**] [Classification: (null)] [Priority: 3] {TCP} 172.21.224.2:49652 -> 142.250.1.139:80
11/23/2022-12:38:58.958203  [**] [1:12345:3] GET on wire [**] [Classification: (null)] [Priority: 3] {TCP} 172.21.224.2:58494 -> 142.250.1.139:80
```

Each line represents an alert generated when traffic matched an alerting rule. Important fields include:

- **Timestamp:** when the event was logged.
- **`[1:12345:3]`:** rule metadata in generator-ID, signature-ID, revision format; here the signature ID is `12345` and revision is `3`.
- **`GET on wire`:** the rule's message.
- **Classification and priority:** alert categorization/priority fields. The example has a null classification and priority 3.
- **`{TCP}`:** the transport protocol shown for the traffic.
- **Source endpoint:** IP address and source port, such as `172.21.224.2:49652`.
- **Destination endpoint:** IP address and destination port, such as `142.250.1.139:80`.
- **`->`:** the displayed source-to-destination direction.

The two example entries have the same rule ID and message but different source ports and timestamps. This is consistent with separate observed traffic instances matching the same rule.

**Quick validation:** Confirm that the alert message and signature ID correspond to your rule, and compare the number of entries after each test. A change in alert count can help evaluate a rule, but the count alone does not prove that the rule is correct; inspect the matching traffic and expected behavior as well.

---

## 6. Task 3 — Examine `eve.json`

### 6.1 View the raw event log

Run:

```bash
cat /var/log/suricata/eve.json
```

This displays the JSON records as raw text. A large event log can be difficult to read this way.

### 6.2 Format JSON with `jq`

Run:

```bash
jq . /var/log/suricata/eve.json | less
```

- `jq .` pretty-prints JSON to make the structure easier to inspect.
- `less` lets you navigate the output page by page.
- Press `f` to move forward and `b` to move backward.
- Press `q` to exit `less`.
- If a command is stuck or you need to interrupt it, press `Ctrl+C` to return to the shell.

The `jq` utility can also select individual fields, filter events, and transform JSON data.

### 6.3 Check the alert severity

The course asks for the value of the `severity` property for the first alert returned by the formatted output.

**Answer from the lab:** `3`.

### 6.4 Extract selected event fields

Run:

```bash
jq -c '[.timestamp, .flow_id, .alert.signature, .proto, .dest_ip]' /var/log/suricata/eve.json
```

This command selects five fields from each JSON event and prints the selected values compactly:

| Field | What it represents |
|---|---|
| `.timestamp` | Event timestamp. |
| `.flow_id` | Identifier used by Suricata to associate events with a network flow. |
| `.alert.signature` | Alert signature/message associated with the event, when present. |
| `.proto` | Protocol recorded for the event. |
| `.dest_ip` | Destination IP address recorded for the event. |

The lab gives this example output:

```json
["2022-11-23T12:38:34.624866+0000",14500150016149,"GET on wire","TCP","142.250.1.139"]
["2022-11-23T12:38:58.958203+0000",1647223379236084,"GET on wire","TCP","142.250.1.102"]
```

**Answer from the lab:** The destination IP address for the last event in the displayed example is `142.250.1.102`.

> The values above are examples from the course material. Your results may differ if you use a different PCAP, rule set, or Suricata configuration.

### 6.5 Identify the first alert signature

The lab's answer is **`GET on wire`** (the course text also renders it once as “GET on WIRE”). It is the value of `.alert.signature` for the first alert event in the supplied example.

### 6.6 Correlate events by `flow_id`

A network flow is a sequence of related packets between endpoints that share characteristics such as IP addresses and protocol. Suricata assigns a `flow_id` so events associated with the same flow can be correlated.

Copy a `flow_id` from the previous command and substitute it for `X` in:

```bash
jq 'select(.flow_id == X)' /var/log/suricata/eve.json
```

For example, if the selected flow ID is `14500150016149`, run:

```bash
jq 'select(.flow_id == 14500150016149)' /var/log/suricata/eve.json
```

This returns event records whose `flow_id` matches the selected value. Correlation can help an analyst examine related events together rather than treating every log line as an isolated observation.

**Practical note:** JSON records do not all necessarily contain alert-specific fields. When examining events that are not alerts, `.alert.signature` may be absent or null. Interpret fields in the context of the event type.

---

## 7. Knowledge check and answers

The following answers reflect the values explicitly given in the supplied lab material.

| Question | Answer |
|---|---|
| What is the `severity` value for the first alert returned by the lab's `jq` output? | `3` |
| What is the destination IP address for the last event in the supplied example? | `142.250.1.102` |
| What is the alert signature for the first alert entry in the supplied example? | `GET on wire` |

## 8. Validation checklist

Use this checklist to confirm that you completed the lab's core tasks.

- [ ] Displayed `custom.rules` and identified its action, header, and options.
- [ ] Explained the purpose of `alert`, `drop`, `pass`, and `reject` in the lab context.
- [ ] Ran Suricata against `sample.pcap` using `custom.rules`.
- [ ] Confirmed that Suricata created output files in `/var/log/suricata/`.
- [ ] Read `fast.log` and identified the signature ID, message, timestamp, source, and destination.
- [ ] Formatted `eve.json` with `jq`.
- [ ] Extracted timestamp, flow ID, signature, protocol, and destination IP fields.
- [ ] Used a real `flow_id` from the output to filter related events.
- [ ] Compared observed alerts with the expected rule behavior.

## 9. Analyst takeaways

1. **Rules turn detection intent into matching conditions.** The action, header, and options work together to define which traffic is matched and what happens when it matches.
2. **A packet capture makes testing repeatable.** Processing `sample.pcap` allows the same traffic to be used when evaluating rule changes.
3. **`fast.log` is convenient for quick inspection.** It provides compact alert summaries, but the lab identifies it as a deprecated format that is not recommended for incident response or threat hunting.
4. **`eve.json` supports richer investigation.** Structured JSON can be filtered and processed programmatically with tools such as `jq`.
5. **Flow correlation adds context.** A shared `flow_id` can connect multiple event records associated with the same network flow.
6. **Validate, do not merely count.** An alert count is useful feedback, but an analyst should also check whether the right traffic matched and whether expected events were missed.

## 10. Lab completion and cleanup

When you are satisfied that the tasks are complete:

1. Select **End Lab** in the course interface.
2. Confirm by selecting **Submit** in the confirmation prompt.
3. Optionally provide a rating or feedback.
4. Close the lab browser tab and return to the course.
5. Refresh the course tab if needed to update completion status.

Ending the lab removes access to the lab shell, so save any notes you need before ending it.

---

## Reference summary

- **Rule file:** `custom.rules`
- **Capture file:** `sample.pcap`
- **Output directory:** `/var/log/suricata/`
- **Quick alert log:** `/var/log/suricata/fast.log`
- **Structured event log:** `/var/log/suricata/eve.json`
- **Rule under examination:** `alert http $HOME_NET any -> $EXTERNAL_NET any (msg:"GET on wire"; flow:established,to_server; content:"GET"; http_method; sid:12345; rev:3;)`
- **Main commands:**

```bash
cat custom.rules
ls -l /var/log/suricata
sudo suricata -r sample.pcap -S custom.rules -k none
cat /var/log/suricata/fast.log
cat /var/log/suricata/eve.json
jq . /var/log/suricata/eve.json | less
jq -c '[.timestamp, .flow_id, .alert.signature, .proto, .dest_ip]' /var/log/suricata/eve.json
jq 'select(.flow_id == 14500150016149)' /var/log/suricata/eve.json
```

*Prepared as structured study notes from the supplied lab text. Example outputs and quiz answers are retained as source-specific examples; actual output depends on the lab data and configuration.*
