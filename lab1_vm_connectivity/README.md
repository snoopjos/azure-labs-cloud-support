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
```
---

### Step 5 – SSH Attempt (Unexpected Success)

Attempted SSH connection to the VM using the public IP, expecting it to fail due to a blocked port.  
However, the connection succeeded — indicating that the default NSG attached to the VM subnet or NIC already had port 22 open.

**Command:**
```bash
ssh azureuser@172.203.144.32
```

---

### Step 6 – Simulate Blocked SSH via NSG Rule

Manually created a Deny rule in the Network Security Group to block inbound SSH traffic on port 22.  
This simulates a real-world NSG misconfiguration that causes connectivity loss to Azure VMs.

**Command:**
```bash
az network nsg rule create \
  --resource-group Lab1ResourceGroup \
  --nsg-name Lab1VMNSG \
  --name DenySSH \
  --priority 100 \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --destination-port-ranges 22 \
  --source-address-prefixes '*' \
  --destination-address-prefixes '*'
```


  --generate-ssh-keys \
  --no-wait

---

### Step 7 – SSH Attempt After NSG Deny Rule (Expected Failure)

Re-attempted SSH to the VM after explicitly blocking port 22 via the NSG.  
The connection timed out, confirming that the rule was effective and simulating a real-world scenario of port misconfiguration.

**Command:**
```bash
ssh azureuser@172.203.144.32
```
