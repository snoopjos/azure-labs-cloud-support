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
### Step 4 – Upload Blob Using Storage Account Key

Uploaded a test file (`testfile.txt`) to the previously created blob container using a **Storage Account Key** for authentication. This step simulates standard upload access prior to the SAS token expiry scenario.

Before uploading the blob, we retrieved the **Storage Account Key** needed for authentication. This method is used in scenarios where role-based access control (RBAC) is insufficient or not granted.

**Retrieve Storage Account Key:**
```bash
az storage account keys list \
  --resource-group learn-9cc8f79c-5d59-4135-b95d-569466e39239 \
  --account-name lab3storage16792 \
  -o table
```

**Command:**
```bash
az storage blob upload \
  --account-name lab3storage16792 \
  --account-key <your-storage-account-key> \
  --container-name lab3container \
  --name testfile.txt \
  --file testfile.txt
```

### Step 5 – Generate SAS Token and Access Blob

Generated a SAS (Shared Access Signature) token that grants **read access** to the uploaded blob for a short duration (1 minute), simulating a real-world expiring link scenario.

✅ Verified access by pasting the full SAS URL into a browser and successfully previewing the text file contents.

**Command to generate the SAS token (with secure key authentication):**
```bash
az storage blob generate-sas \
  --account-name lab3storage16792 \
  --container-name lab3container \
  --name testfile.txt \
  --permissions r \
  --expiry $(date -u -d '1 minute' '+%Y-%m-%dT%H:%MZ') \
  --https-only \
  --account-key <your-storage-account-key> \
  -o tsv

az storage account keys list \
  --resource-group <your-resource-group> \
  --account-name <your-storage-account-name> \
  -o table
```
**Note:** Example SAS URL format: https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>?<SAS-token>

### Step 6 – Simulate Blob Access Denial and Troubleshoot

Using the generated SAS URL from Step 5, an attempt was made to access the blob via a web browser.

❌ The result was an access denial with the following error: 
<Error> 
  <Code>PublicAccessNotPermitted</Code> 
  <Message>Public access is not permitted on this storage account.</Message> 
</Error> 

This outcome simulates a realistic cloud support issue where a user cannot access a blob due to access restrictions. In this case, the error was caused by the storage account’s public access setting being disabled — a default in many secure configurations. The proper support path would involve generating a new SAS token or modifying access control policies depending on the organization's security policies



### Step 7 – Verify Storage Account Public Access Configuration

To investigate the `PublicAccessNotPermitted` error received when accessing the blob via a Shared Access Signature (SAS) URL, we verified the storage account’s public access settings.

**Command Used:**
```bash
az storage account show \
  --name lab3storage16792 \
  --resource-group learn-9cc8f79c-5d59-4135-b95d-569466e39239 \
  --query "allowBlobPublicAccess"
```
Output: false

The output indicates that the storage account has public blob access disabled, which prevents anonymous access to blobs even with a valid SAS token. This is a common security configuration in enterprise environments.

### Step 8 – Attempt to Enable Public Read Access at the Container Level

To simulate a public user accessing the blob file, we attempted to grant **public read access** to the container using the following command:

```bash
az storage container set-permission \
  --name lab3container \
  --public-access blob \
  --account-name lab3storage16792 \
--account-key <your-storage-account-key>
```
Expected: This command should allow unauthenticated (public) users to read blobs within the container by enabling public access at the container level.

Actual: 
<Error>
  <Code>PublicAccessNotPermitted</Code>
  <Message>Public access is not permitted on this storage account.</Message>
</Error>

Explanation: Azure storage accounts now have public access disabled by default as a security measure. Although the container-level permission command executed, the storage account itself is blocking public access, resulting in the error when trying to access the blob using the SAS URL.

Expected Results if allowed: true
Actual Results in Sandbox: false

Explanation: Since the storage account's allowBlobPublicAccess setting is false, container-level settings to allow public access will be overridden and denied

### What We'd Do If Access Was Permitted

If the storage account allowed public access, the following steps would be taken to resolve the blob access issue:

1. **Enable Public Access on the Storage Account**  
   (Only if permitted by organizational policy and not restricted by Azure sandbox):

   ```bash
   az storage account update \
     --name lab3storage16792 \
     --set allowBlobPublicAccess=true
   ```
2. Update Container Access Level
  ```bash
  az storage container set-permission \
      --name lab3container \
      --public-access blob \
      --account-name lab3storage16792
  ```
3. Validate Blob Access in a Browser
   
---
Since the sandbox does not permit enabling public access on the storage account, the remaining steps are theoretical and demonstrate what would be done in a real-world scenario.


### Step 9 – Resolution Summary & Long-Term Recommendations
This final step summarizes the issue encountered, outlines the actions taken to troubleshoot it, and provides recommendations to prevent similar issues in a production environment.

Root Cause
The access denial occurred due to a PublicAccessNotPermitted error returned when attempting to access the blob via a SAS URL. Although the SAS token was generated correctly and the blob container permissions were updated, the storage account's allowBlobPublicAccess setting was configured to false. This account-level restriction prevented any form of anonymous access, overriding the container-level settings and effectively rendering the SAS token unusable for public access.

Support Actions Taken
To investigate the issue, the storage account configuration was examined using Azure CLI commands. We confirmed the allowBlobPublicAccess setting was disabled. Attempts to enable public read access at the container level were unsuccessful due to the account-level restriction. SAS tokens were generated and tested, and the resulting error messages were analyzed to determine the root cause. These steps reflect how a support team would troubleshoot a real-world blob access issue caused by security configurations.

Key Takeaways
Azure now disables public blob access by default to enhance storage security. A valid SAS token is not sufficient on its own to grant access if the storage account is configured to block public access. Successful blob access relies on the alignment of several configuration layers, including token scope and expiration, container-level permissions, and account-level settings. Troubleshooting should follow a logical process: verify token validity, review container permissions, and inspect the storage account’s access policies.

Recommendations for Production Environments
To avoid similar issues in live environments, organizations should avoid using public blob access unless absolutely necessary. Instead, secure access should be managed through properly scoped SAS tokens and Azure role-based access control (RBAC). Storage account policies should be standardized and enforced to ensure consistent security configurations. Additionally, monitoring and alerting for expired or misconfigured SAS tokens can improve operational response. Support teams should maintain detailed internal documentation—such as standard operating procedures (SOPs) or runbooks—so that common access issues can be quickly diagnosed and resolved.
