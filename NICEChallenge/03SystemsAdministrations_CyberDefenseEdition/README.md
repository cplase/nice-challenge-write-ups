# Challenge 03 (#T0180) - Systems Administrations: Cyber Defense Edition
![](../images/challenge03/NICESystemAdmin.png)

## Challenge Info
**Author:** Agustin Castro<br>
**Framework Category:** Protect and Defend<br>
**Specialty Area:** Cybersecurity Defense Infrastructure Support<br>
**Work Role:** Cyber Defense Infrastructure Support Specialist<br>
**Task Description:** Perform system administration on specialized cyber defense applications and systems (e.g., antivirus, audit and remediation) or Virtual Private Network (VPN) devices, to include installation, configuration, maintenance, backup, and restoration.

### Scenario
Our managed service provider has notified us that an important system/application is either missing or no longer functioning properly. This has brought the company's current cyber defense posture below an acceptable baseline. Confirm the system/application that the managed service provider has identified with engineering and then correct the issue as soon as possible.

### Additional Information
More details and objectives about this challenge will be introduced during the challenge meeting, which will start once you begin deploying the challenge.

You will be able to check your progress during this challenge using the check panel within the workspace once the challenge is deployed. The checks within the check panel report on the state of some or all the required tasks within the challenge.

Once you have completed the requested tasks, you will need to document the methodology you used with as much detail and professionalism as necessary. This should be done on the documentation tab within the workspace once the challenge is deployed. Below the main documentation section be sure to include a tagged listed of applications you used to complete the challenge.

Your username/password to access all virtual machines and services within the workspace will be the following...<br>
Username: `playerone`<br>
Password: `password123`

The username/password used to access the Firewall's web interface within the workspace will be the following...<br>
Username: `admin`<br>
Password: `password123`

## Meeting Notes
![](../images/challenge03/meeting_notes.png)

## Network Map
![](../images/challenge03/PD-map.jpg)

## Documentation
The Centipede site's **Edge-Router1** had a misconfiguration for its two IPsec/IKEv2 VPN tunnels. One tunnel was peered with an external MSP (172.31.2.5), while another tunnel was peered with HQ (172.31.2.2).

To begin troubleshooting, I accessed the Security Desk workstation (172.16.20.55) via a VMRC remote session. From there, I accessed the pfSense firewall (172.31.2.2) that was **Edge-Router1**'s peer for the VPN tunnel. Access to the firewall was accomplished via a web GUI interface.

![](../images/challenge03/pfsense.png)

![](../images/challenge03/phase2_config.png)

I noticed that the pfSense firewall also had a VPN peering with the MSP, so I noted the configuration down (along with the peering information for Centipede's router).

I then shifted my attention to **Edge-Router1** and accessed it via a VMRC remote session. The router ran VyOS for its operating system.

The VPN tunnels used IPsec/IKEv2--which utilizes two Phases for a successful configuration. To check and see if Phase 1 was configured correctly, I ran the command `show vpn ike`:

![](../images/challenge03/show_vpn_ike.png)

Phase 1 configurations were correct--as shown by the `up` state in the output. Phase 2 configurations, however, were incorrect--based on the output of `show vpn ipsec sa`:

![](../images/challenge03/show_vpn_ipsec_sa.png)

Further diagnostic information seemed to indicate two issues:

1. The IPsec ESP group profile--for both tunnel configurations--has an incorrect local network prefix set to 192.168.100.0/24.
2. The IPsec ESP group profile--for both tunnel configurations--has an incorrect PFS cryptography setting.

![](../images/challenge03/show_vpn_ipsec_sa_detail_peer_172.31.2.2.png)

```
$ monitor vpn ipsec
  VPN-IPSEC: "peer-172.31.2.2-tunnel-1" #47: ignoring informational payload, type INVALID_HASH_INFORMATION
  VPN-IPSEC: "peer-172.31.2.2-tunnel-1" #61: max number of retransmissions (2) reached STATE_QUICK_I1. No acceptable response to our first Quick Mode message: perhaps peer likes no proposal
  VPN-IPSEC: "peer-172.31.2.5-tunnel-1" #55: cannot respond to IPsec SA request because no connection is known for 192.168.10.0/24===172.31.2.3[172.31.2.3]...172.31.2.5[172.31.2.5]
```

```
$ show vpn ipsec policy
src 192.168.100.0/24 dst 172.31.2.5/32
        dir out priority 3875 ptype main
        tmpl src 0.0.0.0 dst 0.0.0.0
                proto esp reqid 0 mode transport
src 192.168.100.0/24 dst 172.16.30.0/24
        dir out priority 3907 ptype main
        tmpl src 0.0.0.0 dst 0.0.0.0
                proto esp reqid 0 mode transport
```

To resolve the issue, the following configuration changes were made:

```
edit vpn ipsec esp-group ESP-1W 
set pfs dh-group5
edit vpn ipsec site-to-site peer 172.31.2.2 tunnel 1 local
set prefix 192.168.10.0/24
edit vpn ipsec site-to-site peer 172.31.2.5 tunnel 1 local
set prefix 192.168.10.0/24
```

Configurations were then committed and saved with the following commands:

```
commit
save
```

## NICE Framework & CAE KU Mapping
### NICE Framework KSA
* K0001. Knowledge of computer networking concepts and protocols, and network security methodologies.
* K0004. Knowledge of cybersecurity and privacy principles.
* K0005. Knowledge of cyber threats and vulnerabilities.
* K0033. Knowledge of host/network access control mechanisms (e.g., access control list, capabilities lists).
* K0044. Knowledge of cybersecurity and privacy principles and organizational requirements (relevant to confidentiality, integrity, availability, authentication, non-repudiation).
* K0104. Knowledge of Virtual Private Network (VPN) security.
* K0221. Knowledge of OSI model and underlying network protocols (e.g., TCP/IP).
* K0332. Knowledge of network protocols such as TCP/IP, Dynamic Host Configuration, Domain Name System (DNS), and directory services.
* K0334. Knowledge of network traffic analysis (tools, methodologies, processes).
* S0059. Skill in using Virtual Private Network (VPN) devices and encryption.
* S0077. Skill in securing network communications.
* S0121. Skill in system, network, and OS hardening techniques. (e.g., remove unnecessary services, password policies, network segmentation, enable logging, least privilege, etc.).

### CAE Knowledge Units
* Advanced Network Technology and Protocols
* Basic Networking
* Cybersecurity Foundations
* Cybersecurity Principles
* Network Defense
* Operating Systems Administration
* Operating Systems Concepts
