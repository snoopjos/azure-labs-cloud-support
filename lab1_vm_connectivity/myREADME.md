## Step 1: Create Resource Group
Deployed a new RG using Azure CLI in portal to my Github Practice subscription.

### Command Used
```bash
az group create --name Lab1RG --location uksouth
```
-----
## Step 2: Create a VM in RG1
Created a VM in RG1 but came across a ton of issues. Needed to add --generate-ssh-keys
flag and --size Standard_B1s to my command. If this is the first time creating a VM you have to add the SSH flag to generate a key pair. This is only needed the first time as it will then use the existing key pair in the future.
Size had to be added as the VM size Standard_DS1_v2 isn’t currently available in the uksouth region.

### Command Used
```bash
az vm create --resource-group Lab1RG  --name Lab1VM --image Ubuntu2204 --admin-username azureuser --generate-ssh-keys --size Standard_B1s
```
-----
## Step 3: Find VM public IP
Obtained the public VM in order to SSH to it. This step confirms the VM is externally addressable and helps verify connectivity before any troubleshooting begins.

### Command Used
```bash
az vm list-ip-addresses   --name Lab1VM   --resource-group Lab1RG   --query "[].virtualMachine.network.publicIpAddresses[].ipAddress"   -o tsv
```

-----
### Step 4 – SSH Attempt (Unexpected Success)

Attempted SSH connection to the VM using the public IP, expecting it to fail due to a blocked port.  
However, the connection succeeded — indicating that the default NSG attached to the VM subnet or NIC already had port 22 open.

**Command:**
```bash
ssh azureuser@20.254.95.32
```

-----