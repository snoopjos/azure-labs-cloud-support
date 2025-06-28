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

### Step 3 – Storage Account Creation (Permission Denied)

Attempted to create a new storage account inside the sandbox resource group to simulate an RBAC issue.

Azure returned an `AuthorizationFailed` error due to insufficient role permissions (`Microsoft.Storage/storageAccounts/write`) on the current user account. This accurately reflects a real-world cloud support scenario where a user is assigned an incorrect RBAC role such as "Reader" instead of "Contributor."

**Command:**
```bash
az storage account create \
  --name lab2storage$RANDOM \
  --resource-group Lab2ResourceGroup \
  --location eastus \
  --sku Standard_LRS
```
