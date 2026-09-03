# port-scanning-pcap-analysis
CyberLab-10



You are a  SOC analyst and the SOC team sent you this log file saying that they noticed something malicious.  
Write a report about this log file.

<img width="500" height="303" alt="morpheus_normal_traffic" src="https://github.com/user-attachments/assets/996cfd56-bf2d-4ee3-b37d-9c041f0e04a3" />

---

## #Overview

The objective of this investigation is to analyze the provided Wireshark PCAP file, identify relevant Indicators of Compromise (IOCs), and assess whether the observed behavior represents malicious activity.  

---

## #Incident Scenario

As a SOC analyst, the investigation focuses on answering the following questions:

- What caused the suspicious activity?
- Which host initiated the activity?
- Which host was targeted?
- Which ports and services were targeted?
- What scanning technique was used?
- Was the activity successful?
- Does the activity represent a True Positive or False Positive?

---

## #Incident Analysis

- After initial scrolling of the .PCAP file, it was observed 2103 packets and 2100 between two local devices.
  
<img width="1893" height="656" alt="image" src="https://github.com/user-attachments/assets/1be3b4e0-f9a5-4260-9deb-c2ba160b6ad4" />
<img width="1529" height="345" alt="image" src="https://github.com/user-attachments/assets/36b29bc6-76ef-4695-8d2f-0f81cb26d8f9" />

- Apply the following filter will show that (192.168.1.6) send many SYN messages on different ports to (192.168.6.2)

  ```bash
  ip.addr==192.168.1.6 && ip.addr==192.168.1.2
  ```

  <img width="1874" height="543" alt="image" src="https://github.com/user-attachments/assets/4f92cef3-9d34-4416-9894-3a0db2c8f6de" />




- This traffic pattern appears to be port-scanning activity so, we will follow the TCP stream of a well known port and for a random port to see the conversation

  **RDP Port 3389 (Known)**

<img width="1759" height="168" alt="image" src="https://github.com/user-attachments/assets/abf4e431-7d09-4cca-8258-7a8fb3b34339" />


  **Port 8888 (Random)**

  <img width="1701" height="212" alt="image" src="https://github.com/user-attachments/assets/68e38b45-ebd5-4b2a-9db7-ed77201a9558" />



-  To identify the scan type, we will use the packets using the following  filter:

```bash
tcp.flags.syn ==1 && tcp.flags.ack ==1
```
<img width="1888" height="502" alt="image" src="https://github.com/user-attachments/assets/ab170c59-0eb7-4c06-a03b-631dc1149e2b" />


- At this stage of the investigation, we have confirmed that active network scanning is taking place. The traffic pattern is consistent with a TCP SYN scan, with ports (135,139,445,3389) open.

---

## #Findings

The observed traffic demonstrates activity associated with network port scanning.

The source host generated multiple connection attempts against different destination ports on the target system.

This behavior can indicate an attempt to identify:

- Open ports
- Accessible network services
- Potential attack surfaces
- Services that could potentially be exploited

Based on the packet behavior, the scanning activity is **TCP SYN Scan**.

TCP SYN Scan (commonly referred to as a half-open scan or stealth scan) is an active network reconnaissance technique used to discover open ports on a target host without completing the full TCP three-way handshake.

**Notes:** 

**Normal 3-Way handshake**  

<p align="center">
<img width="500" height="200" alt="image" src="https://github.com/user-attachments/assets/9ec73a6c-00ea-498a-af6e-48c5930235a3" />  
</p>


**TCP SYN Scan**
- When the Port is OPEN (Half-Open Scan), The attacker sends a SYN packet, the target responds with SYN-ACK (proving it is open), and the attacker immediately replies with a RST (Reset) packet to kill the connection before it fully opens.
 - When the Port is CLOSED, The attacker sends a SYN packet, the target immediately rejects it with a RST (Reset) packet, and the attacker sends no reply at all because the target has already shut down the conversation.

<p align="center">
<img width="600" height="240" alt="image" src="https://github.com/user-attachments/assets/b629bc18-a9ea-4937-ac33-159af7306281" />
</p>

---

## #Indicators of Compromise

- What caused the suspicious activity?  
  Active Reconnaissance (Port Scanning)
  
- Which host initiated the activity?  
  Source-IP: 192.168.1.6
  
- Which host was targeted?  
  Dst-IP: 192.168.1.2
  
- Which ports and services were targeted?  
  135,139,445,3389
  
- What scanning technique was used?  
  TCP SYN Scan
  
- Was the activity successful?  
  Yes
  
- Does the activity represent a True Positive or False Positive?  
  True Positive

  
---

## #MITRE ATT&CK Mapping

The observed reconnaissance activity can be mapped to:

**T1046 — Network Service Scanning**

https://attack.mitre.org/techniques/T1046/

This technique involves scanning systems to identify available network services and potential attack surfaces.
The observed behavior is consistent with this technique because the source system attempted to communicate with multiple ports on the target host.  

---

## #Recommended Actions

- At the time of analysis, the observed activity appears to represent reconnaissance rather than confirmed exploitation.
- Port scanning by itself does not demonstrate that a system was compromised. However, it can represent an early stage of an attack.
- The risk level should therefore be determined based on the target's importance and the source of the traffic.

The SOC team should consider the following actions:

- Search the SIEM for additional activity from the same source.
- Ensure that there is no open unused ports. 
- Review firewall and IDS/IPS logs for related connections.
- Determine whether other hosts were targeted.
- Block or restrict the source if the activity is confirmed to be unauthorized.


----

## #Lessons Learned

This investigation demonstrates the importance of correlating network logs with packet-level evidence.
While logs can identify suspicious connection attempts, Wireshark provides additional visibility into the underlying network behavior including TCP flags.

