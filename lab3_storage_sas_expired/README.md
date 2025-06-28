# Lab 3 – Azure Storage SAS Token Expiry Simulation

This lab simulates a real-world L1/L2 cloud support issue where a user loses access to an Azure Storage blob due to an expired Shared Access Signature (SAS) token. The goal is to demonstrate how to investigate and resolve storage access issues by identifying token expiration and renewing access.

---

### Step 1 – Sandbox Setup & Resource Group Context

This lab uses the **Azure Learn Sandbox**, which provides a temporary subscription and resource group with limited permissions.

✅ Verified the active subscription and resource group:
- **Subscription ID:** auto-generated (sandbox-managed)
- **Resource Group:** `learn-9cc8f79c-5d59-4135-b95d-569466e39239`

**Command:**
```bash
az group list -o table
```

### Step 2 – Storage Account Deployment

Deployed a new Storage Account using Azure CLI within the sandbox-provided resource group. This account will be used to simulate restricted blob access scenarios.

✅ Verified:
- Provisioning state was `Succeeded`
- Endpoint URLs (Blob, File, Queue, Table) were generated

**Command:**
```bash
az storage account create \
  --name lab3storage16792 \
  --resource-group learn-9cc8f79c-5d59-4135-b95d-569466e39239 \
  --location westus \
  --sku Standard_LRS
```
### Step 3 – Create Blob Container

Created a blob container within the newly provisioned storage account. This container will host the test blob file we’ll later access using a SAS token.

✅ Verified that the container was created successfully and listed within the storage account.

**Command:**
```bash
az storage container create \
  --account-name lab3storage16792 \
  --name lab3container \
  --auth-mode login
```
