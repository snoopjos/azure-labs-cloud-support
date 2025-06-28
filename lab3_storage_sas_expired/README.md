# Lab 3 – Azure Storage SAS Token Expiry Simulation

This lab simulates a real-world L1/L2 cloud support issue where a user loses access to an Azure Storage blob due to an expired Shared Access Signature (SAS) token. The goal is to demonstrate how to investigate and resolve storage access issues.

---

### Step 1 – Resource Group Initialization

Deployed a new resource group for the lab.

**Command:**
```bash
az group create --name Lab3ResourceGroup --location eastus
```
