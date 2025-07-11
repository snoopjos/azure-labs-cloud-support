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
### Step 2 – Create VNet2 with Subnet2

Created a second Virtual Network (`VNet2`) in the `westus` region with an associated subnet named `Subnet2`. This VNet will be peered with `VNet1` to enable inter-VNet communication.

✅ Verified successful creation and confirmed the address space is non-overlapping with VNet1.

**Command:**
```bash
az network vnet create \
  --name VNet2 \
  --resource-group VNetPeeringLabRG \
  --location westus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Subnet2 \
  --subnet-prefix 10.1.1.0/24
```
### Step 3 – Create VNet Peering (VNet1 ↔ VNet2)

Established bidirectional VNet peering between `VNet1` and `VNet2` to allow communication between their respective subnets.

✅ Verified peering connections were successfully created in both directions.

**Commands:**
```bash
az network vnet peering create \
  --name VNet1ToVNet2 \
  --resource-group VNetPeeringLabRG \
  --vnet-name VNet1 \
  --remote-vnet VNet2 \
  --allow-vnet-access

az network vnet peering create \
  --name VNet2ToVNet1 \
  --resource-group VNetPeeringLabRG \
  --vnet-name VNet2 \
  --remote-vnet VNet1 \
  --allow-vnet-access
```

### Step 4 – Verify VM and Subnet Deployment

In this step, we verified that both virtual machines and their associated subnets were created successfully inside the correct virtual networks.

First, we listed all virtual machines within the resource group to confirm that `VM1` and `VM2` were provisioned correctly.

**Command:**
```bash
az vm list --resource-group VNetPeeringLabRG -o table
```
Next, we confirmed that each virtual network (VNet1 and VNet2) contained the expected subnets (Subnet1 and Subnet2).

Commands: 
``` bash
# List VNet1 subnets
az network vnet subnet list \
  --resource-group VNetPeeringLabRG \
  --vnet-name VNet1 \
  -o table

# List VNet2 subnets
az network vnet subnet list \
  --resource-group VNetPeeringLabRG \
  --vnet-name VNet2 \
  -o table
```
Verification showed both subnets and both VMs were provisioned and assigned to the correct virtual networks

### Step 5 – Create and Assign Network Security Groups (NSGs)
To simulate traffic control between the two peered VNets, we created and applied Network Security Groups (NSGs) to each subnet. This mimics real-world scenarios where L1/L2 support engineers must verify whether connectivity issues are caused by network-level security restrictions.

We created two NSGs (VNet1-NSG and VNet2-NSG) and added inbound rules to allow traffic from the virtual network. These NSGs were then associated with their respective subnets.

✅ Verified successful creation of NSGs, rule assignments, and subnet associations.

Create NSGs: 
``` bash
az network nsg create \
  --resource-group VNetPeeringLabRG \
  --name VNet1-NSG \
  --location westus

az network nsg create \
  --resource-group VNetPeeringLabRG \
  --name VNet2-NSG \
  --location westus
```
Add inbound allow rules:
```
az network nsg rule create \
  --resource-group VNetPeeringLabRG \
  --nsg-name VNet1-NSG \
  --name AllowVNetInBound \
  --protocol '*' \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix VirtualNetwork \
  --source-port-range '*' \
  --destination-address-prefix '*' \
  --destination-port-range '*' \
  --access Allow

az network nsg rule create \
  --resource-group VNetPeeringLabRG \
  --nsg-name VNet2-NSG \
  --name AllowVNetInBound \
  --protocol '*' \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix VirtualNetwork \
  --source-port-range '*' \
  --destination-address-prefix '*' \
  --destination-port-range '*' \
  --access Allow
```
Associate NSGs with subnets: 
``` bash
az network vnet subnet update \
  --vnet-name VNet1 \
  --name Subnet1 \
  --resource-group VNetPeeringLabRG \
  --network-security-group VNet1-NSG

az network vnet subnet update \
  --vnet-name VNet2 \
  --name Subnet2 \
  --resource-group VNetPeeringLabRG \
  --network-security-group VNet2-NSG
```
