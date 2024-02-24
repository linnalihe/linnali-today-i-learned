---
draft: false
date: 2024-02-21 12:25
tags:
  - iac
internal-links:
---
NOTE: Use Windows as host machine to follow along. Do not use MacOS M1
##### 41 Vagrant IP, RAM, and CPU

Learned so far: Vagrant cloud where vm images are stored. Vagrantfile where vm settings are written. Vagrant commands such as `up`, `status`, `ssh`, `halt`

Table of contents for this section
- Vagrant Networking: allocate ip addresses
- Provisioning: executing commands through Vagrantfile when vm comes up
- RAM, CPU: change these setting in Vagrantfile
- Multivm: have multiple vms set up in a Vagrantfile
- Documentation

When setting private network, chose `56` for the second to last block of number. `56` recognized for virtual box
