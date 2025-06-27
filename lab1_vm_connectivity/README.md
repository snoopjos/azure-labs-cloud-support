# Lab 1 – Azure VM Connectivity Failure (NSG Blocked Port)

This lab simulates a real-world L1 cloud support issue where a virtual machine becomes unreachable due to an improperly configured Network Security Group (NSG). The goal is to identify the root cause (blocked port 22 for SSH) and remediate it using Azure CLI.

---

### Step 2 – Resource Group Initialization

Deployed a new resource group to scope and contain all related resources for this lab.

**Command**
```bash
az group create --name Lab1ResourceGroup --location eastus
