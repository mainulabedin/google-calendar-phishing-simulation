# Google Calendar Attack Simulation and Detection

## 1. Introduction
The objective of this lab is to simulate a Google Calendar Phishing Attack utilizing malicious `.ics` invites and subsequently detect the malicious behavior using the Wazuh SIEM platform. 

In this threat scenario, an attacker masquerades as an internal IT Support Team and dispatches a spoofed calendar invitation (`.ics` file) embedded with a link to a malicious payload. Once the targeted victim executes this payload, a Metasploit Meterpreter reverse TCP shell session is established, granting the attacker complete remote administrative control over the Windows victim endpoint.

---

## 2. Environment Setup & Network Topology
The attack simulation and detection architecture are constructed within an isolated virtual environment using the following parameters:

*   **Attacker Node:** Ubuntu 20.04 VM | **IP Address:** `192.168.241.147`
*   **Victim Endpoint:** Windows 10 VM (Configured with Wazuh Agent & Sysmon) | **IP Address:** `192.168.241.151`
*   **SIEM Infrastructure:** Kali Linux (Hosting the centralized Wazuh Manager)
*   **Network Layout:** NAT.

---

## 3. Step-by-Step Attack Implementation

### Step 1: Framework Installation & Payload Generation
Log into the **Ubuntu Attacker Machine**, install the Metasploit Framework via snap package management, and leverage `msfvenom` to compile a Windows reverse TCP executable payload:
<img width="980" height="304" alt="image" src="https://github.com/user-attachments/assets/a4bfe447-f49c-415a-9fd3-2a6718bbd209" />


### Step 2: Hosting the Malicious Payload via Apache Web Server
Install and start an Apache2 web server to host the payload, moving the compiled executable into the web root directory.
<img width="851" height="108" alt="image" src="https://github.com/user-attachments/assets/265b7c78-d0f1-4528-9604-e015b190c5d8" />
<img width="881" height="163" alt="image" src="https://github.com/user-attachments/assets/cfc58ffc-7c91-42e9-91c7-6f70582585d4" />


### Step 3: Verifying Remote Availability
The attacker verifies that the malicious executable is accessible over the network via a web browser download interface.
<img width="877" height="381" alt="image" src="https://github.com/user-attachments/assets/116a7873-1257-427d-8643-73eb4fe354ee" />


### Step 4: Embedding Malicious Indicators in the ICS Template
Configure the iCalendar payload fields (`LOCATION` and `DESCRIPTION`) with high-urgency text prompting the user to download the hosted malware.
<img width="980" height="37" alt="image" src="https://github.com/user-attachments/assets/2e5fab17-a866-4ab0-af4d-90f42203a34c" />
<img width="811" height="506" alt="image" src="https://github.com/user-attachments/assets/3f6c2607-d979-4346-86d1-ae0261bb7e61" />


### Step 5: Configuring Mail Spoofing Parameters
The script is populated with the authenticated user credentials, the spoofed sender metadata, and the target email address destination.
<img width="951" height="30" alt="image" src="https://github.com/user-attachments/assets/8ec371a1-98c9-4364-b4a4-f6d378c47c36" />
<img width="766" height="539" alt="image" src="https://github.com/user-attachments/assets/8851c64c-c9f1-4b3d-a447-1e98b4cec1cf" />
<img width="791" height="477" alt="image" src="https://github.com/user-attachments/assets/59f8ad87-5a8d-4289-bc89-15b4288903b5" />
<img width="823" height="341" alt="image" src="https://github.com/user-attachments/assets/df29a45a-3aa4-446e-a588-457a1b0c6595" />


### Step 6: Executing the Phishing Script
Execute the completed Python script on the `Ubuntu machine` to dispatch the malicious email.
<img width="980" height="59" alt="image" src="https://github.com/user-attachments/assets/4ef64df3-b754-4b4f-8f91-94084be77dda" />


### Step 7: Email Arrival at Target Spam Folder
On the Windows Victim Endpoint, the incoming spoofed communication successfully traverses initial gateway filters and establishes a presence inside the target's email system under the subject heading `"Urgent Security Update Required"`.
<img width="878" height="428" alt="image" src="https://github.com/user-attachments/assets/67bc6081-6d8e-4dd4-8f5d-abe72f0d0cc3" />


### Step 8: Initializing the Metasploit Framework Listener
On Ubuntu Machine: Now open a new terminal tab and fire up `msfconsole` to prepare the receiving handler.
<img width="980" height="365" alt="image" src="https://github.com/user-attachments/assets/6c1140b0-fb09-4863-adac-be816ad308c5" />


### Step 9: Configuring the Multi-Handler Matrix
Setup the reverse listener, matching the structural properties (`LHOST`, `LPORT` and `payload` architecture) previously encoded into the malware executable.
<img width="808" height="207" alt="image" src="https://github.com/user-attachments/assets/7142db85-6c5c-4867-ab7c-9fddcc7d0314" />


### Step 10: Interacting with the E-mail and Attachment and also Verifying the Download Status
The victim opens the malicious email message and triggers a download of the attached `.ics` file asset.
<img width="727" height="351" alt="image" src="https://github.com/user-attachments/assets/3f6e18fe-0b7c-46e2-bef2-4b5b250ca1db" />
<img width="577" height="244" alt="image" src="https://github.com/user-attachments/assets/ef49e3d9-a1f7-4b4e-8737-3d385471a8f4" />


### Step 11: Attempting the Calendar Update Integration
The email client automatically generates a UI widget prompting the victim to `"Add to Calendar"` because of the integrated `.ics` file structure.
</br>
<img width="630" height="475" alt="image" src="https://github.com/user-attachments/assets/d3289495-b429-4583-8af3-87547e2b5cba" />


### Step 12: Triggering Malware Delivery via Browser Redirect
The victim clicks the embedded calendar link, causing the web browser to reach out to the Apache server and pull down the `rasd.exe` payload binary.
</br>
<img width="637" height="456" alt="image" src="https://github.com/user-attachments/assets/81109413-3c87-44ad-b000-e32278aa7545" />
<img width="567" height="286" alt="image" src="https://github.com/user-attachments/assets/b078f5b2-289d-4591-97d1-81afe79f6413" />


### Step 13: Privilege Escalation & Execution
The end-user navigates to their local execution target zone (`\Downloads`), right-clicks the newly acquired `rasd.exe` binary, and selects `"Run as administrator"`, completing the deployment phase of the attack chain
<img width="453" height="373" alt="image" src="https://github.com/user-attachments/assets/02c4fea2-d2a5-4a7a-b5f1-4364d49343ea" />


## 4. SIEM Detection & Log Analysis (Wazuh)

### Step 14: Verifying Windows Log Decoders
Before writing custom rules, the directory is checked to confirm that the default Windows log decoders are available within the `Wazuh` ruleset structure.
<img width="872" height="85" alt="image" src="https://github.com/user-attachments/assets/b0c74453-0060-466a-9a7f-455b96799378" />


### Step 15: Accessing the Local Rules Configuration File
The `local_rules.xml` configuration file is opened using the `nano` text editor to begin implementing custom detection logic tailored to this specific threat model.
<img width="980" height="56" alt="image" src="https://github.com/user-attachments/assets/e1b91f13-97fb-4beb-a0ff-ff4092665473" />


### Step 16: Authoring Rules for Initial Execution and Network Beaconing

Incorporate the custom rules into your `/var/ossec/etc/rules/local_rules.xml` file. These rules are tailored to detect initial execution behaviors, network beaconing, process injection, and composite multi-stage attack patterns:

*   **Rule 100200 (Level 9):** Evaluates `sysmon_event1` (Process Creation) to flag any suspicious executable launched out of the user's `Downloads` directory.
*   **Rule 100201 (Level 12):** Evaluates `sysmon_event3` (Network Connection) to flag outbound network connections specifically initiated by the `rasd.exe` binary.
*   **Rule 100202 (Level 13):** Flags network connections routing explicitly to the known attacker Command and Control (C2) IP address and port combo (`192.168.241.147:4444`).
*   **Rule 100203 (Level 14):** Evaluates `sysmon_event8` (CreateRemoteThread) to catch process injection attempts originating from `rasd.exe`.
*   **Rule 100204 (Level 15):** A composite correlation rule that triggers a critical alert only when a Downloads-based execution (`100200`) and a subsequent connection to the C2 address (`100202`) occur within a 180-second window.
<img width="697" height="428" alt="image" src="https://github.com/user-attachments/assets/782c3403-bb41-4673-ac1f-a48201e7bca7" />
<img width="725" height="372" alt="image" src="https://github.com/user-attachments/assets/62dea546-6c34-43e9-bbe5-99e9492e5c7b" />
<img width="760" height="431" alt="image" src="https://github.com/user-attachments/assets/2a68226f-099e-4f84-9c69-56701c72c5d6" />
<img width="980" height="320" alt="image" src="https://github.com/user-attachments/assets/dc0d74d6-643e-475f-a4e3-dd7c08ae8ef3" />


### Step 17: Wazuh SIEM Dashboard Overview
The Wazuh dashboard metrics update to show that high-severity events have been intercepted. The dashboard clearly registers 3 alerts that are Level 12 or above.
</br>
<img width="671" height="381" alt="image" src="https://github.com/user-attachments/assets/548b1461-cbec-484a-b186-e9858011c8f0" />


### Step 18: Analysing Triggered Security Alerts

Navigating to the Wazuh Security Alerts event logs table confirms successful end-to-end detection matching the simulated attack vector. The correlation engine effectively captured the lifecycle of the threat:

*   **Rule 92213 (Level 15):** Triggered automatically when the malicious payload was initially dropped into a directory commonly abused by malware vectors.
*   **Rule 100204 (Level 15):** Successfully matched our custom multi-stage correlation rule, explicitly verifying the entire phishing-to-C2 reverse shell execution chain within the specified window.
*   **Rule 61640 (Level 12):** Captures the post-exploitation phase, detecting the stealthy process injection routine into `explorer.exe`.
<img width="980" height="342" alt="image" src="https://github.com/user-attachments/assets/5ccc3000-bef7-47c8-a999-4a80dde62c49" />
