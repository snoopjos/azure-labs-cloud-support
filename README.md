# Azure Cloud Support Engineering Labs

This repository contains hands-on labs that simulate real-world L1/L2 Azure support engineering scenarios. Each lab replicates common issues faced in production environments and includes detailed remediation steps using the Azure CLI.

## Labs Completed

### Lab 1 – VM Connectivity Failure (NSG Blocked Port)
Simulates a virtual machine becoming unreachable due to a misconfigured Network Security Group blocking port 22. The issue is diagnosed and resolved using CLI-based troubleshooting.
Folder: [lab1_vm_connectivity](./lab1_vm_connectivity)

### Lab 2 – RBAC Misassignment (Permission Denied)
Simulates a permissions issue when attempting to create a resource with insufficient RBAC privileges. Root cause analysis and proper resolution are documented.
Folder: [lab2_rbac_permission_denied](./lab2_rbac_permission_denied)

## Repository Structure

Each lab directory contains:
- A README file with step-by-step documentation
- Azure CLI commands and outputs
- Root cause and resolution notes
- Screenshots demonstrating each stage of the issue and fix

## Purpose

These labs are designed to demonstrate practical Azure troubleshooting, scripting, and documentation skills. They reflect day-to-day responsibilities of entry-level cloud engineers, particularly in L1/L2 support roles using Microsoft Azure.
