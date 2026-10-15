# Activity: Compare Wireshark and tcpdump

## Activity Overview

In this activity, you will compare two network protocol analyzers: **Wireshark** and **tcpdump**. The goal is to understand how they work, what features they provide, and when a security analyst might choose one over the other.

A **network protocol analyzer**, also called a **packet sniffer**, captures and analyzes network traffic. Security analysts use these tools to inspect communication between systems, troubleshoot connectivity issues, investigate suspicious activity, and understand network protocols.

> **Activity objective:** Identify at least **two differences** and at least **three similarities** between Wireshark and tcpdump.

## Scenario

You are a cybersecurity analyst. You have been asked to research Wireshark and tcpdump and create a comparison chart that explains their differences and similarities.

## Step-by-Step Instructions

### Step 1: Research the tools

Use official documentation and reliable technical references to learn about both tools:

- [tcpdump documentation and resources](https://www.tcpdump.org/index.html#documentation)
- [tcpdump manual page (Linux man-pages)](https://man7.org/linux/man-pages/man8/tcpdump.8.html)
- [Wireshark User’s Guide](https://www.wireshark.org/docs/wsug_html/)
- [Wireshark: Filtering packets while viewing](https://www.wireshark.org/docs/wsug_html_chunked/ChWorkDisplayFilterSection)

As you research, consider the following questions:

- What software or equipment is required to use the tool?
- Is it open-source or proprietary?
- What type of user interface does it provide?
- How do analysts capture, analyze, and filter network traffic with it?
- What are its recommended use cases and limitations?

### Step 2: Complete the comparison chart

The chart below is a completed example you can use to understand the expected response.

## Wireshark vs. tcpdump: Comparison Chart

| Feature | Wireshark | tcpdump |
|---|---|---|
| **Interface** | Primarily uses a graphical user interface (GUI), with menus, a packet list, packet details, and packet bytes panes. | Uses a command-line interface (CLI); results are displayed as text in the terminal. |
| **Main strength** | Offers an interactive environment for detailed packet inspection, protocol dissection, colorized packet views, and statistics. | Offers lightweight, efficient packet capture and text-based inspection, making it useful on remote servers and systems without a desktop environment. |
| **Filtering** | Supports **capture filters** to limit what is collected and **display filters** to control which captured packets are shown. Display filters can be applied after capture without removing packets from the capture file. | Uses packet-filter expressions to select traffic during capture or when reading a capture file. Options such as `-i`, `-r`, and `-w` control the interface, input file, and output file. |
| **Reviewing packets** | Presents packet summaries and lets users expand protocol layers and inspect decoded fields and packet bytes interactively. | Prints packet summaries and selected protocol details in text form; options such as `-v` and `-X` provide more detail or hexadecimal/ASCII output. |
| **Saving captures** | Can save captured traffic to a file for later analysis and can open existing capture files. | Can write packets to a capture file with `-w` and read a saved file with `-r`. |
| **Typical use cases** | Best suited to interactive investigations, learning protocols, exploring packet fields, examining conversations, and using built-in statistics. | Best suited to quick command-line checks, remote troubleshooting, scripted captures, and collecting traffic on servers with limited resources. |
| **Resources and access** | Usually requires installation of the Wireshark application and a graphical environment for its full interactive experience. Packet capture permissions and capture support depend on the operating system and configuration. | Usually installed or installable through a system package manager on Unix-like systems. It generally has a small command-line footprint, but packet capture often requires elevated privileges or specific capabilities. |
| **Limitations and considerations** | The volume of captured traffic can be large and complex; GUI-based analysis may be less convenient over a low-bandwidth remote terminal. Capturing traffic may require appropriate permissions. | Text output can be less convenient for exploring complex protocol structures and comparing many fields visually. Users need to be comfortable with command-line options and filter expressions. |

## At Least Three Similarities

1. **Both are network protocol analyzers.** They capture and inspect packets so analysts can investigate network communication and troubleshoot problems.
2. **Both support traffic filtering.** Each can restrict the packets captured or examined to focus on relevant traffic, such as a particular host, port, or protocol.
3. **Both can save and read packet captures.** Captured traffic can be stored for later examination; tcpdump uses options such as `-w` and `-r`, while Wireshark can save and open capture files.
4. **Both help with troubleshooting and security analysis.** Analysts can use either tool to investigate unexpected connections, protocol behavior, and other network events.
5. **Both may require appropriate permissions for live capture.** The exact requirements depend on the operating system and how packet-capture permissions are configured.

## Example Commands

### tcpdump: list available capture interfaces

```bash
sudo tcpdump -D
```

### tcpdump: capture a small number of packets

```bash
sudo tcpdump -i eth0 -c 10 -nn
```

- `-i eth0`: capture on interface `eth0`.
- `-c 10`: stop after processing 10 packets.
- `-nn`: do not resolve host addresses or port numbers to names.

Use the interface name available on your own system; it may not be `eth0`.

### tcpdump: save packets for later analysis

```bash
sudo tcpdump -i eth0 -w capture.pcap
```

Stop the capture with **Ctrl+C**. Read the saved file with:

```bash
tcpdump -nn -r capture.pcap
```

### Wireshark: example display filters

Enter these in Wireshark’s display-filter bar:

```text
tcp
```

Shows packets containing TCP.

```text
dns
```

Shows DNS packets.

```text
ip.addr == 192.168.1.10
```

Shows packets where the specified IPv4 address is a source or destination.

> **Important distinction:** Wireshark capture filters determine which packets are collected; display filters determine which packets are shown from the captured data. Applying a display filter does not delete hidden packets from the capture file.

## Which Tool Should an Analyst Choose?

- Choose **Wireshark** when you need interactive, visual, protocol-level analysis or want to explore packet fields and statistics.
- Choose **tcpdump** when you need a quick terminal-based capture, remote-server troubleshooting, or a lightweight way to save traffic for later analysis.
- In many investigations, the tools complement one another: capture traffic with tcpdump on a remote system, save it to a file, and then open the file in Wireshark for detailed visual analysis.

## Conclusion

Wireshark and tcpdump both help analysts capture, filter, and inspect network traffic, but they offer different workflows. Wireshark emphasizes interactive graphical analysis, while tcpdump emphasizes command-line capture and text output. The best choice depends on the investigation, the system being examined, available resources, and the analyst’s workflow.

## References

1. The Tcpdump Group. [tcpdump documentation and resources](https://www.tcpdump.org/index.html#documentation).
2. Linux man-pages project. [tcpdump(8) manual](https://man7.org/linux/man-pages/man8/tcpdump.8.html).
3. Wireshark Foundation. [Wireshark User’s Guide](https://www.wireshark.org/docs/wsug_html/).
4. Wireshark Foundation. [Filtering packets while viewing](https://www.wireshark.org/docs/wsug_html_chunked/ChWorkDisplayFilterSection).

## Submission Checklist

- [x] At least two differences between Wireshark and tcpdump are included.
- [x] At least three similarities between the tools are included.
- [x] The comparison covers interface, filtering, analysis, use cases, and limitations.
- [ ] Save a blank copy of the course template separately for future practice.
