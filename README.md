Secufi
A network security and access-management solution for small
businesses.

Secufi is a project focused on improving the security of small-business
networks by separating trusted and guest devices, controlling guest
access, and monitoring suspicious network activity. It is designed
around an affordable, router-based deployment that can be adapted to
shops and other small organizations.

Project status: Prototype / development
Documentation status: Initial documentation --- update the
implementation details as each component is configured and tested.
Table of Contents
- 1. Project Overview
- 2. Problem Statement
- 3. Objectives
- 4. Proposed Architecture
- 5. Core Components
- 6. Network Segmentation
- 7. Guest Network Access
- 8. Intrusion Detection and
  Monitoring
- 9. Technology Stack
- 10. Deployment Overview
- 11. Testing and Validation
- 12. Repository Structure
- 13. Security Considerations
- 14. Current Limitations and Future
  Work
- 15. Contributing
- 16. Disclaimer
1. Project Overview
Small businesses often use a single Wi-Fi network for employees,
business systems, and visitors. This can expose internal devices and
services to unnecessary risk when guest devices are connected to the
same network.

Secufi aims to address this problem by introducing network separation,
controlled guest access, and network traffic monitoring through a
centralized router-based setup.

The project explores the integration of:
- OpenWrt as the central router and network-management platform.
- VLANs to logically separate internal and guest devices.
- NoDogSplash to provide a captive portal for guest-network
  access.
- Snort IDS to inspect network traffic and identify suspicious
  activity.
The exact capabilities available in a deployment depend on the
configuration and testing completed for each component.
2. Problem Statement
In a typical small-business network, guest users and internal devices
may share the same network. This can increase the risk of:
- Unauthorized access to internal devices.
- Lateral movement between connected devices.
- Exposure of local services and shared resources.
- Unmonitored or suspicious network activity.
- Poor control over guest Wi-Fi usage.
Secufi investigates a practical way to reduce these risks without
requiring a complex enterprise-grade network infrastructure.
3. Objectives
1. Separate internal business devices from guest devices.
2. Restrict unnecessary communication between network segments.
3. Provide a captive portal for guest-network access.
4. Monitor network traffic for suspicious patterns.
5. Keep the architecture affordable and manageable for small
   businesses.
6. Document the configuration, testing, and security decisions so the
   system can be reproduced.
4. Proposed Architecture
The proposed deployment uses OpenWrt as the central network gateway.
Separate VLANs carry internal and guest traffic. Firewall rules control
communication between those networks, while the guest network can use a
captive portal for access management. Snort can be deployed at a
suitable monitoring point to inspect traffic.
                         Internet
                            |
                      [ISP / Modem]
                            |
                      [OpenWrt Router]
                            |
              +-------------+-------------+
              |                           |
        Internal VLAN                Guest VLAN
              |                           |
      Business Devices              Guest Devices
      PCs / Printers                 Phones / Laptops
                                          |
                                   Captive Portal
                                   (NoDogSplash)

              Network traffic monitoring
                       (Snort IDS)
This is a logical overview, not a final physical wiring diagram. The
actual interface names, VLAN IDs, addressing, firewall zones, and
Snort monitoring interface must be documented for the deployed
configuration.

5. Core Components
  Component                           Responsibility
  OpenWrt                             Central router, interface
configuration, DHCP, firewall, and
network management

  VLANs                               Logical separation of internal and
guest traffic

  NoDogSplash                         Captive-portal access control for
the guest network
Snort IDS                           Network traffic inspection and
detection of configured suspicious
patterns
OpenWrt
OpenWrt is the router operating system used as the foundation of the
network. It provides the configuration layer for network interfaces,
VLANs, DHCP services, and firewall policies.
VLANs
Virtual LANs divide a physical network into logically isolated broadcast
domains. Secufi uses this capability to separate trusted internal
devices from guest devices.
NoDogSplash
NoDogSplash is a captive-portal solution intended for the guest network.
It can present an access page to users before allowing normal network
access. Any collection of guest information must be implemented with
appropriate notice, consent, and data protection.
Snort IDS
Snort is a network intrusion detection system. Secufi uses it as the
planned traffic-monitoring component for identifying configured
suspicious patterns, such as selected port scans or unusual connection
attempts.

Important: Detection and prevention are different capabilities. An
IDS alerts on matching traffic; blocking requires an explicitly
configured enforcement mechanism and must be tested separately.
6. Network Segmentation
The network is intended to contain at least two logical segments:
  Segment                 Intended devices        Access policy
  Internal network        Business computers,     Access to approved
printers, and trusted   internal services and
devices                 the internet
Guest network           Customer and visitor    Internet access only;
devices                 no access to internal
devices
Configuration details to record
Fill in these values after finalizing the router configuration:

  Setting                         Value
  Internal VLAN ID                TBD
Guest VLAN ID                   TBD
Internal subnet                 TBD
Guest subnet                    TBD
Router / gateway address        TBD
DHCP range --- internal         TBD
DHCP range --- guest            TBD
Physical / logical interfaces   TBD
Segmentation validation
- Confirm that devices receive addresses from the correct subnet.
- Confirm that each device uses the intended default gateway.
- Confirm that guest devices cannot reach internal devices.
- Confirm that approved internet access works from both segments.
- Test IPv4 and IPv6 policies if IPv6 is enabled.
- Record the firewall rules and the results of each test.
7. Guest Network Access
The guest network is intended to use NoDogSplash as its captive portal.

Expected access flow:
1. A guest connects to the guest Wi-Fi network.
2. The device receives an IP address from the guest DHCP scope.
3. The guest is redirected to the captive portal, where supported by
   the client and configuration.
4. The guest completes the configured access process.
5. The guest receives the network access permitted by the guest
   firewall policy.
Document the following when implemented:
- Captive-portal landing page and terms.
- Authentication or access method.
- Session duration and timeout policy.
- Any information collected and its retention period.
- How portal access is revoked or reset.
Do not store personal information in plaintext or collect phone numbers
without a clear purpose, notice, and appropriate consent.
8. Intrusion Detection and Monitoring
Snort is intended to monitor selected network traffic and generate
alerts for configured suspicious behavior.

Potential test cases include:
- ICMP traffic between selected network segments.
- TCP SYN scan patterns.
- TCP NULL scan patterns.
- Attempts to reach restricted internal services from the guest
  network.
For each test, document:

  Field             Description
  Test ID           Unique identifier
Source            Originating test device or subnet
Destination       Target device or subnet
Traffic pattern   Type of traffic generated
Expected result   Expected alert, drop, or allowed traffic
Actual result     Observed behavior
Evidence          Relevant Snort alert, log, or packet capture

Run security tests only on networks and devices you own or are
explicitly authorized to assess.
9. Technology Stack
  Technology           Purpose
  OpenWrt              Router and network management
VLAN / IEEE 802.1Q   Network segmentation
NoDogSplash          Captive portal
Snort                Intrusion detection
GNS3                 Network topology design and simulation
Linux                Administration, testing, and scripting

Versions, packages, and configuration dependencies should be recorded in
the deployment notes once the environment is finalized.
10. Deployment Overview
The following is a high-level implementation sequence. It is not a
substitute for device-specific configuration instructions.
1. Prepare the environment: identify router hardware, interfaces,
   firmware, and network requirements.
2. Configure OpenWrt: establish WAN and LAN connectivity and
   confirm administrative access.
3. Create VLANs: configure internal and guest network interfaces.
4. Configure DHCP and firewall policies: assign separate address
   ranges and restrict inter-segment traffic.
5. Configure guest access: install and configure NoDogSplash on the
   guest network.
6. Configure monitoring: install Snort and select an appropriate
   traffic-monitoring interface.
7. Validate the deployment: test connectivity, isolation, portal
   behavior, and IDS alerts.
8. Record the results: preserve sanitized configuration examples,
   test cases, and screenshots or logs.
Deployment notes
Before publishing configuration files, remove passwords, private keys,
API tokens, public IP addresses where sensitive, and other secrets.
Replace environment-specific values with clearly marked placeholders.
11. Testing and Validation
Testing should verify both intended connectivity and intended
restrictions.
  Area                                Example validation
  DHCP                                Internal and guest clients receive addresses from their respective scopes

  Gateway                             Each client uses the correct
default gateway

  Isolation                           Guest clients cannot access
internal hosts or services

  Internet access                     Permitted internet connectivity works

  Captive portal                      Guest clients encounter the
configured portal flow

  IDS                                 Test traffic produces the expected
Snort alert
Firewall                            Restricted traffic is handled
according to the documented policy
Add test dates, software versions, command output, and evidence
references as tests are completed. Do not mark a test as passed until
its result has been observed.
12. Repository Structure
A suggested documentation-friendly layout is:
Secufi/
├── README.md
├── docs/
│   ├── architecture.md
│   ├── network-design.md
│   ├── openwrt-configuration.md
│   ├── vlan-and-firewall.md
│   ├── captive-portal.md
│   ├── snort-ids.md
│   └── testing-and-validation.md
├── configs/
│   ├── openwrt/
│   ├── snort/
│   └── nodogsplash/
├── diagrams/
├── test-results/
└── scripts/
This is a suggested structure. Keep only directories that exist in the
repository, and do not commit secrets or unredacted user data.
13. Security Considerations
- Apply least-privilege firewall rules.
- Deny guest-to-internal access by default, then allow only explicitly
  required traffic.
- Keep router firmware and installed packages updated.
- Use strong administrative credentials and restrict management
  access.
- Protect configuration backups and monitoring logs.
- Avoid collecting unnecessary personal information through the
  captive portal.
- Define log retention and access policies.
- Test IPv6 and other alternate paths so they do not bypass intended
  segmentation.
- Treat IDS alerts as evidence for investigation, not automatic proof
  of compromise.
14. Current Limitations and Future Work
The following are potential development tasks. Update this section to
reflect the actual project status.
- Finalize and document the VLAN and IP addressing plan.
- Validate guest-to-internal isolation across all relevant traffic
  paths.
- Complete and test the NoDogSplash guest-access workflow.
- Tune Snort rules to reduce false positives.
- Evaluate whether selected alerts should trigger firewall-based
  blocking.
- Develop a secure method for handling any guest registration data.
- Add repeatable deployment and validation scripts.
- Add network diagrams, sanitized configuration examples, and test
  evidence.
- Evaluate centralized logging and a simple monitoring dashboard.
15. Contributing
Contributions, testing feedback, documentation improvements, and
suggestions are welcome.

When contributing:
1. Explain the purpose of the proposed change.
2. Include relevant configuration or test evidence.
3. Avoid committing credentials, personal information, or sensitive
   network details.
4. Update the documentation when behavior or configuration changes.
16. Disclaimer
Secufi is a network-security project intended for educational, research,
and authorized deployment purposes. Test only systems and networks for
which you have explicit permission. The project does not guarantee
complete protection against cyber threats; security depends on correct
configuration, maintenance, and ongoing validation.
Documentation Maintenance
Keep this README aligned with the actual implementation. For every major
component, record:
- Software and firmware version.
- Hardware and interface details where relevant.
- Configuration changes and their purpose.
- Commands or steps required to reproduce the setup.
- Validation procedure and observed result.
- Known issues and workarounds.
Last reviewed: 2026-11-08
