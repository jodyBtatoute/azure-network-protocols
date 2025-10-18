<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Video Demonstration</h2>

- ### [YouTube: Azure Virtual Machines, Wireshark, and Network Security Groups](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Step 1
- Step 2
- Step 3
- Step 4

<h2>Actions and Observations</h2>

When you **capture and analyze network traffic to and from Azure Virtual Machines (VMs)** using **Wireshark** and **experiment with Network Security Groups (NSGs)**, you can observe several important networking behaviors. Let’s break this down into two parts — what you *see in Wireshark*, and what happens when you *modify NSG rules*.

---

### 🧠 1. Observations with Wireshark on Azure VM Traffic

When you run **Wireshark** on an Azure VM (or a machine communicating with it), you can capture and inspect packets across various protocols. Common observations include:

#### a. **Types of Traffic**

* **ICMP (Ping)**: You’ll see `Echo Request` and `Echo Reply` messages when testing connectivity (e.g., `ping <VM-IP>`).
* **TCP Traffic**: Common for protocols like HTTP (port 80), HTTPS (port 443), RDP (port 3389), or SSH (port 22).

  * You’ll observe the **TCP three-way handshake** (`SYN`, `SYN-ACK`, `ACK`) when a connection is established.
  * If NSGs block a port, you’ll see **SYN packets without a response**, or **RST (Reset)** messages if the connection is rejected.
* **UDP Traffic**: You may capture stateless traffic (e.g., DNS queries or streaming data) without acknowledgments.
* **ARP Traffic** (if inside the same subnet): Resolving IP to MAC addresses.

#### b. **Traffic Direction**

* **Inbound**: Packets arriving at the VM (e.g., remote client accessing a web server).
* **Outbound**: Packets leaving the VM (e.g., the VM reaching out to an API, updating packages, etc.).

#### c. **Latency and Packet Loss**

Wireshark shows **response times** and potential **packet drops**, which can be correlated with NSG rules or Azure firewall configurations.

---

### 🔐 2. Observations When Experimenting with NSGs

A **Network Security Group (NSG)** in Azure acts like a virtual firewall at the subnet or network interface level. It uses *rules* that allow or deny traffic based on:

* Source/destination IP or CIDR
* Port number
* Protocol (TCP, UDP, ICMP)
* Direction (inbound/outbound)
* Priority number

#### Example Experiments and What You’ll See:

| **Experiment**                                      | **Expected Observation in Wireshark**                                                                                                                |
| --------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| Allow all inbound traffic (default deny overridden) | All packets reach the VM; normal TCP handshakes complete successfully.                                                                               |
| Block inbound RDP (port 3389)                       | RDP client sends SYN packets, but you see no SYN-ACK replies; connection eventually times out.                                                       |
| Allow only ICMP                                     | Only ping requests and replies appear; TCP or UDP packets are dropped (Wireshark shows one-way traffic).                                             |
| Deny all outbound traffic                           | You’ll see packets from the VM attempting to leave, but no responses — outgoing SYNs or DNS queries fail.                                            |
| Apply NSG at subnet vs. NIC                         | Depending on scope, traffic may be blocked before it even reaches the VM’s NIC (you’ll see no packets on the VM if subnet-level NSG denies traffic). |

---

### 📈 3. Combining Wireshark and NSG Insights

When used together:

* **Wireshark** shows you *what’s happening at the packet level*.
* **NSGs** let you *control what traffic is allowed to reach or leave* the VM.

By enabling or disabling NSG rules and monitoring with Wireshark, you can **validate that Azure’s network filtering behaves as expected**.

---

### ✅ Example Summary Table

| **Scenario**         | **NSG Rule**                | **Wireshark Observation**                   |
| -------------------- | --------------------------- | ------------------------------------------- |
| Ping allowed         | Allow ICMP inbound/outbound | ICMP Echo Request/Reply visible             |
| SSH blocked          | Deny TCP 22 inbound         | SYN packets with no response                |
| Web traffic allowed  | Allow TCP 80/443 inbound    | Normal TCP handshake and data exchange      |
| All outbound blocked | Deny all outbound           | Outgoing SYN or DNS queries without replies |

---

Would you like me to show how to **set up a simple experiment** in Azure (e.g., using one VM as client, another as server, and NSG testing steps)?
