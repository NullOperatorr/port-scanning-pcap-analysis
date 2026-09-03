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


---

## #Findings

The observed traffic demonstrates activity associated with network port scanning.

The source host generated multiple connection attempts against different destination ports on the target system.

This behavior can indicate an attempt to identify:

- Open ports
- Accessible network services
- Potential attack surfaces
- Services that could potentially be exploited

Based on the packet behavior, the scanning activity is TCP SYN Scan.

TCP SYN Scan (commonly referred to as a half-open scan or stealth scan) is an active network reconnaissance technique used to discover open ports on a target host without completing the full TCP three-way handshake.

**Note** 

**Normal 3-Way handshake**  

<img width="600" height="240" alt="image" src="https://github.com/user-attachments/assets/9ec73a6c-00ea-498a-af6e-48c5930235a3" />  


**TCP SYN Scan**
- When the Port is OPEN (Half-Open Scan), The attacker sends a SYN packet, the target responds with SYN-ACK (proving it is open), and the attacker immediately replies with a RST (Reset) packet to kill the connection before it fully opens.
 - When the Port is CLOSED, The attacker sends a SYN packet, the target immediately rejects it with a RST (Reset) packet, and the attacker sends no reply at all because the target has already shut down the conversation.

   <img width="600" height="240" alt="image" src="https://github.com/user-attachments/assets/c5ba0ad2-292e-447b-887c-f7d1519904c0" />

