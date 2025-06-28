# Lab 4 – Azure VNet Peering & Subnet Communication

This lab demonstrates how to configure Virtual Network (VNet) peering and test communication between two subnets in separate VNets. It's designed to simulate a real-world networking scenario relevant to L1/L2 cloud support roles, especially for identifying and resolving connectivity issues across peered networks.

---

### Step 1 – Create Resource Group and VNet1 with Subnet1

Created a new resource group and deployed the first Virtual Network (VNet1) in the westus region with a subnet named Subnet1.

✅ Verified both resources were created successfully.

**Command:**
```bash
az group create \
  --name VNetPeeringLabRG \
  --location westus
az network vnet create \
  --name VNet1 \
  --resource-group VNetPeeringLabRG \
  --location westus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.1.0/24
```
