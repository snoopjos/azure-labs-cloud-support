# Lab 1 – Azure VM Connectivity Failure (NSG Blocked Port)

This lab simulates a real-world L1 cloud support issue where a virtual machine becomes unreachable due to a misconfigured Network Security Group (NSG). The goal is to identify the root cause (port 22 for SSH is blocked) and remediate it using Azure CLI.

---

### Step 1 – Azure Authentication

This lab was performed in **Azure Cloud Shell**, which is already authenticated by default.  
If running locally, the following command would be required to initiate an authenticated session:

```bash
az login
```

### Step 2 – Resource Group Initialization

Deployed a new resource group to scope and contain all related resources for this lab.

**Command**
```bash
az group create --name Lab1ResourceGroup --location eastus
```

### Step 3 – Virtual Machine Provisioning

Deployed an Ubuntu 22.04 LTS VM named `Lab1VM` in the previously created resource group.  
Port 22 (SSH) was intentionally left closed to simulate a connectivity failure due to NSG misconfiguration.

**Command**
```bash
az vm create \
  --resource-group Lab1ResourceGroup \
  --name Lab1VM \
  --image Ubuntu2204 \
  --admin-username azureuser \
```
---

### Step 4 – Extract VM Public IP

Retrieved the public IP assigned to `Lab1VM` using Azure CLI. This step confirms the VM is externally addressable and helps verify connectivity before any troubleshooting begins.

**Command:**
```bash
az vm list-ip-addresses \
  --name Lab1VM \
  --resource-group Lab1ResourceGroup \
  --query "[].virtualMachine.network.publicIpAddresses[].ipAddress" \
  -o tsv


  --generate-ssh-keys \
  --no-wait
```
