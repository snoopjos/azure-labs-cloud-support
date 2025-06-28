# Lab 2 – RBAC Permission Denied

This lab simulates a common Azure support scenario where a user is unable to access a resource due to incorrect role assignment. The issue is diagnosed and resolved using Azure CLI by modifying the user's role.

---

### Step 1 – Lab Folder Initialization

A dedicated GitHub folder was created for Lab 2. All documentation, screenshots, and runbook details for this lab are maintained here.

### Step 2 – Resource Group Creation

Deployed a new resource group to scope all Lab 2 resources.

**Command:**
```bash
az group create --name Lab2ResourceGroup --location eastus
```

