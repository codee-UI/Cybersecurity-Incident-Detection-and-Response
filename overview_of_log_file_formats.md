# Overview of Log File Formats

## Introduction

Logs record events that occur on a network or computer system. In cybersecurity, logs provide important details about activities across an organization, such as who signed in to an application and when the login occurred.

**Log analysis** is the process of examining logs to identify events of interest, investigate suspicious activities, and detect potential security threats.

Security analysts need to understand different log formats to extract useful information and identify unusual or malicious behavior.

This reading covers five common log formats:

1. JavaScript Object Notation (JSON)
2. Syslog
3. Extensible Markup Language (XML)
4. Comma-Separated Values (CSV)
5. Common Event Format (CEF)

---

## 1. JavaScript Object Notation (JSON)

JSON is a lightweight file format used to store and transmit data. It is commonly used in web technologies and cloud environments.

JSON syntax includes the following components:

- Key-value pairs
- Commas
- Double quotes
- Curly brackets
- Square brackets

### Key-Value Pairs

A key-value pair consists of a key and its corresponding value, separated by a colon.

Example:

```json
{
  "Alert": "Malware",
  "Alert code": 1090,
  "severity": 10
}
```

In this example:
- `Alert` is a key, and `Malware` is its value.
- `Alert code` is a key, and `1090` is its numeric value.
- `severity` is a key, and `10` is its numeric value.

### Commas

Commas separate individual key-value pairs in an object and individual elements in an array.

Example:

```json
{
  "Alert": "Malware",
  "Alert code": 1090,
  "severity": 10
}
```

### Double Quotes

Double quotes enclose string values and property names.

Example:

```json
{
  "Alert": "Malware",
  "Alert code": 1090
}
```

The value `"Malware"` is a string, while `1090` is a number and does not require quotation marks.

### Curly Brackets

Curly brackets `{}` enclose a JSON object containing key-value pairs.

Example:

```json
{
  "User": {
    "id": "1234",
    "name": "user",
    "role": "engineer"
  }
}
```

The `User` object contains three properties: `id`, `name`, and `role`.

### Square Brackets

Square brackets `[]` enclose an array, which stores an ordered collection of values.

Example:

```json
{
  "Groups": [
    "Administrators",
    "Users",
    "Engineering"
  ]
}
```

### Key Takeaway

JSON is useful for storing structured data in a readable format. Security analysts often encounter JSON logs in cloud services, applications, APIs, and security monitoring tools.

---

## 2. Syslog

Syslog is a standard used for logging and transmitting log messages. The term can refer to a protocol, a service, or a log message format.

### Syslog Protocol

The syslog protocol transports log messages to centralized logging servers.

Common ports include:

- **Port 514:** Commonly used for plaintext syslog.
- **Port 6514:** Commonly used for syslog over TLS encryption.

### Syslog Service

A syslog service receives log entries from multiple sources and forwards them to a centralized server for monitoring and analysis.

### Syslog Log Format

A traditional syslog message commonly contains three components:

1. Header
2. Structured data
3. Message

Example:

```text
<236>1 2022-03-21T01:11:11.003Z virtual.machine.com evntslog - ID01 [user@32473 iut="1" eventSource="Application" eventID="9999"] This is a log entry!
```

### Header

The header contains information about the logged event.

In this example:

- **Priority:** `<236>`
- **Version:** `1`
- **Timestamp:** `2022-03-21T01:11:11.003Z`
- **Hostname:** `virtual.machine.com`
- **Application:** `evntslog`
- **Process ID:** `-`
- **Message ID:** `ID01`

The timestamp contains the date, time, milliseconds, and timezone. The `Z` indicates Coordinated Universal Time (UTC).

### Structured Data

Structured data provides additional information in a structured format.

Example:

```text
[user@32473 iut="1" eventSource="Application" eventID="9999"]
```

It contains key-value information such as the event source and event ID.

### Message

The message contains the detailed description of the event.

Example:

```text
This is a log entry!
```

### Priority (PRI)

The priority field is enclosed in angle brackets.

Example:

```text
<236>
```

In traditional syslog priority calculations, the PRI value combines a facility and a severity level:

`PRI = Facility × 8 + Severity`

A lower severity number represents a more urgent event. The PRI value alone should not be interpreted as the severity number because it also includes the facility.

### Key Takeaway

Syslog is widely used to collect and forward logs from network devices, servers, and applications. It can also transport logs that use formats such as JSON, XML, and CEF.

---

## 3. Extensible Markup Language (XML)

XML is a markup language used to store and transmit structured data. It is commonly encountered in Windows event logs.

XML uses three important components:

- Tags
- Elements
- Attributes

### Tags

Tags identify and enclose data.

A start tag uses angle brackets, while an end tag includes a forward slash.

Example:

```xml
<EventID>4688</EventID>
```

Here:
- `<EventID>` is the start tag.
- `4688` is the enclosed data.
- `</EventID>` is the end tag.

### Elements

An XML element consists of a start tag, its content, and an end tag.

XML documents typically contain a root element that encloses child elements.

Example:

```xml
<Event>
    <EventID>4688</EventID>
    <Version>5</Version>
</Event>
```

In this example:
- `<Event>` is the root element.
- `<EventID>` is a child element with the value `4688`.
- `<Version>` is a child element with the value `5`.

### Attributes

Attributes provide additional information about an element. They appear inside the start tag and have quoted values.

Example:

```xml
<EventData>
    <Data Name="SubjectUserSid">S-2-3-11-160321</Data>
    <Data Name="SubjectUserName">JSMITH</Data>
    <Data Name="SubjectDomainName">ADCOMP</Data>
    <Data Name="SubjectLogonId">0x1cf1c12</Data>
    <Data Name="NewProcessId">0x1404</Data>
</EventData>
```

In the first `Data` element:

- `Data` is the element name.
- `Name` is an attribute.
- `SubjectUserSid` is the attribute value.
- `S-2-3-11-160321` is the element's content.

### Key Takeaway

XML organizes information through nested elements and attributes. Security analysts may use XML to investigate Windows events, authentication activity, and process creation.

---

## 4. Comma-Separated Values (CSV)

CSV is a text-based format that uses commas to separate data values.

CSV logs may include column headers, but some log files contain only data. Analysts must understand the field order defined by the source device or application.

Example:

```csv
2009-11-24T21:27:09.534255,ALERT,192.168.2.7,1041,x.x.250.50,80,TCP,ALLOWED,1:2001999:9,"ET MALWARE BTGrab.com Spyware Downloading Ads",1
```

This example contains fields representing information such as:

- Timestamp
- Alert type
- Source IP address
- Source port
- Destination IP address
- Destination port
- Protocol
- Action taken
- Signature identifier
- Alert description
- Additional numeric information

The exact meaning of each field depends on the logging system that generated the entry.

### Important Considerations

- CSV fields are generally separated by commas.
- Fields may appear in a fixed order.
- Column headers may be missing.
- Values containing commas may be enclosed in double quotes.
- The source system's documentation helps analysts interpret each field correctly.

### Key Takeaway

CSV is simple and widely supported. It is useful for exporting logs, reviewing tabular data, and analyzing events with spreadsheet or data-analysis tools.

---

## 5. Common Event Format (CEF)

Common Event Format (CEF) is a logging format that uses a standardized structure to identify important event fields.

CEF entries commonly appear in this format:

```text
CEF:Version|Device Vendor|Device Product|Device Version|Signature ID|Name|Severity|Extension
```

The first seven fields are separated by pipe characters (`|`). The extension contains additional information represented as key-value pairs.

### Example CEF Log

```text
Sep 29 08:26:10 host CEF:1|Security|threatmanager|1.0|100|worm successfully stopped|10|src=10.0.0.2 dst=2.1.2.2 spt=1232
```

### Field Breakdown

- **Syslog timestamp:** `Sep 29 08:26:10`
- **Syslog hostname:** `host`
- **CEF version:** `CEF:1`
- **Device vendor:** `Security`
- **Device product:** `threatmanager`
- **Device version:** `1.0`
- **Signature ID:** `100`
- **Name:** `worm successfully stopped`
- **Severity:** `10`
- **Extension:** `src=10.0.0.2 dst=2.1.2.2 spt=1232`

### Extension

The extension contains additional details about the event.

In this example:

- `src=10.0.0.2` identifies the source IP address.
- `dst=2.1.2.2` identifies the destination IP address.
- `spt=1232` identifies the source port.

### Interpreting the Event

The log reports that the security product `threatmanager` successfully stopped a worm. The event has a reported severity of `10`, and the extension provides source, destination, and port information.

The exact meaning of the severity value depends on the CEF implementation and the logging product.

### Key Takeaway

CEF makes it easier to standardize and correlate security events from different products. It is commonly used with security information and event management (SIEM) systems.

The syslog prefix and CEF extension are optional in applicable implementations.

---

## Comparison of Log Formats

| Format | Structure | Common Uses |
|---|---|---|
| JSON | Objects, arrays, and key-value pairs | Cloud services, applications, APIs |
| Syslog | Header, structured data, and message | Unix/Linux systems, network devices, centralized logging |
| XML | Tags, elements, and attributes | Windows events and structured application data |
| CSV | Comma-separated fields | Data exports, spreadsheets, network and security logs |
| CEF | Pipe-separated header fields and key-value extensions | Security products and SIEM platforms |

---

## Key Takeaways

- There is no single standard format for all logs.
- Different systems and applications generate logs in different formats.
- JSON organizes information using objects, arrays, and key-value pairs.
- Syslog is used to format and transport log messages.
- XML represents data using nested elements and attributes.
- CSV stores data in comma-separated fields.
- CEF standardizes security event information using a defined header and extension.
- Understanding log formats helps security analysts identify suspicious behavior and investigate security incidents.

## Additional Resources

- **The Syslog Protocol:** Learn more about syslog message structure and priority levels.
- **Test Data Generator Tool:** Explore an open-source tool for generating sample log data.
- **Date and Time on the Internet: Timestamps:** Learn more about timestamp formats and their interpretation.