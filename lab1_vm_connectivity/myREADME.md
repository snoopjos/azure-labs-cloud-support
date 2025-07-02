## Step 1: Create Resource Group
Deployed a new RG using Azure CLI in portal to my Github Practice subscription.

### Command Used
```bash
az group create --name Lab1RG --location uksouth
```

## Step 2: Create a VM in RG1
Created a VM in RG1 but came across a ton of issues. Needed to add --generate-ssh-keys
flg and --size Standard_B1s to my command. Accordign to Co-pilot I need a SSH key pair and size had to be added as the VM size Standard_DS1_v2 isn’t currently available in the uksouth region.

### Command Used
```bash
az vm create --resource-group Lab1RG  --name Lab1VM --image Ubuntu2204 --admin-username azureuser --generate-ssh-keys --size Standard_B1s
```