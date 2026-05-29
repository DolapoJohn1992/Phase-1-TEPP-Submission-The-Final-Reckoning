# Phase 1 TEPP Submission — The Final Reckoning

## Overview
This repository contains the Phase 1 TEPP (Technical Exploitation & Postmortem) submission for the TKH Innovation Fellowship 2026 Cybersecurity track. The project demonstrates structured reconnaissance, vulnerability identification, exploitation analysis, and defensive remediation across a simulated multi-network environment.

---

## Repository Structure
The_Final_Reckoning/
└── week-12/
└── tepp_postmortem.md

---

## Objectives
- Perform network reconnaissance across segmented environments
- Identify misconfigured services and security weaknesses
- Document exploitation pathways and forensic evidence
- Apply remediation strategies to reduce attack surface
- Evaluate defensive controls from an attacker and defender perspective

---

## Key Findings
- Exposed Redis service without authentication
- Weak service and permission configurations on internal hosts
- Limited segmentation effectiveness between network tiers
- Command injection and access control weaknesses in simulated environment

---

## Security Controls Implemented
- Service-level authentication enforcement (Redis hardening)
- Network access restriction via firewall rules
- Process termination of unauthorized services
- File permission hardening using least privilege principles

---

## Tools & Technologies
- Nmap
- Docker
- Redis
- iptables
- Linux CLI tools (bash, chmod, ps, netstat)

---

## Learning Outcomes
This exercise demonstrates how simple misconfigurations can escalate into critical security breaches. It reinforces the importance of baseline hardening, proper segmentation, and continuous monitoring in enterprise environments.

---

## Author
Dolapo John  
Cybersecurity Analyst (Training)  
TKH Innovation Fellowship 2026
