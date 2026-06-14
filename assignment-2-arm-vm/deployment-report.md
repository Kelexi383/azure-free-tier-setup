# Azure ARM Template VM Deployment — Report
**Programme:** 3MTT (Three Million Technical Talent) Learning Programme  
**Platform:** Darey.io  
**Submitted by:** Kelechi Anuforo  
**Date:** June 12, 2026  

---

## 1. Overview

This report documents the deployment of a Ubuntu Virtual Machine on Microsoft Azure using an Azure Resource Manager (ARM) template. The project demonstrates Infrastructure as Code (IaC) principles by defining all cloud resources declaratively in a JSON configuration file, rather than provisioning them manually through the Azure Portal.

---

## 2. ARM Template Structure

The ARM template (`azuredeploy.json`) follows the standard ARM schema and contains four core sections:

### Parameters
Dynamic inputs that allow the template to be reused across environments:

| Parameter | Value | Description |
|-----------|-------|-------------|
| `vmName` | mtt-ubuntu-vm | Name of the Virtual Machine |
| `adminUsername` | azureuser | VM administrator username |
| `adminPassword` | (secure) | VM administrator password |
| `location` | westus2 | Azure deployment region |
| `vmSize` | Standard_B2s_v2 | VM compute size |
| `ubuntuOSVersion` | 22_04-lts | Ubuntu OS version |

### Variables
Reusable values defined once and referenced throughout the template:
- Virtual Network name, Subnet name, NSG name, NIC name, Public IP name
- Address prefixes (10.0.0.0/16 for VNet, 10.0.0.0/24 for Subnet)
- OS disk type (Standard_LRS)
- Auto-generated DNS label using `uniqueString()` function

### Resources
Five resources deployed in dependency order:
1. Public IP Address
2. Network Security Group (NSG)
3. Virtual Network + Subnet
4. Network Interface Card (NIC)
5. Virtual Machine

### Outputs
Values returned after successful deployment:
- VM name
- Hostname (FQDN)
- SSH connection command

---

## 3. Networking Infrastructure

### Virtual Network (VNet)
- **Name:** mtt-vnet  
- **Address Space:** 10.0.0.0/16  
- **Subnet:** mtt-subnet (10.0.0.0/24)

### Public IP Address
- **Name:** mtt-public-ip  
- **SKU:** Standard  
- **Allocation:** Static  
- **DNS Label:** Auto-generated using `uniqueString()` ARM function

### Network Interface (NIC)
- **Name:** mtt-nic  
- Connects the VM to the subnet and assigns the public IP

---

## 4. Security Controls — Network Security Group (NSG)

The NSG (`mtt-nsg`) was attached to the subnet and configured with the following inbound rules:

| Rule Name | Priority | Protocol | Port | Action | Description |
|-----------|----------|----------|------|--------|-------------|
| Allow-SSH | 1000 | TCP | 22 | Allow | SSH remote access |
| Allow-HTTP | 1100 | TCP | 80 | Allow | HTTP web traffic |
| Deny-All-Inbound | 4096 | Any | Any | Deny | Default deny all other traffic |

This implements a **least-privilege security model** — only explicitly permitted traffic is allowed, and all other inbound connections are denied.

---

## 5. Resource Dependencies

The ARM template uses `dependsOn` attributes to ensure resources are created in the correct order:

```
Public IP ──────────────────────────────────┐
                                             ▼
NSG ──────────────────────────────────────► VNet ──► NIC ──► VM
```

- The **VNet** depends on the **NSG** (NSG must exist before attaching to subnet)
- The **NIC** depends on both the **Public IP** and **VNet**
- The **VM** depends on the **NIC**

This ensures Azure never attempts to create a resource before its dependencies exist.

---

## 6. Compute Resource Configuration

| Property | Value |
|----------|-------|
| **VM Name** | myUbuntuVM |
| **Location** | West US 2 |
| **VM Size** | Standard_B2s_v2 |
| **OS** | Ubuntu Server 22.04 LTS |
| **OS Disk** | Standard_LRS (Standard HDD) |
| **Authentication** | Password-based |
| **Resource Group** | 3mtt-azure-workspace-rg2 |

---

## 7. Parameterization

All environment-specific values were extracted into parameters, enabling the template to be reused for different environments (dev, staging, production) by simply supplying a different parameters file.

A separate `azuredeploy.parameters.json` file stores the parameter values, following the separation of concerns principle — the template defines *what* to deploy, while the parameters file defines *how* to configure it.

---

## 8. Deployment Execution

The deployment was executed through the **Azure Portal Custom Deployment** interface:

**Steps followed:**
1. Navigated to `portal.azure.com` → searched "Deploy a custom template"
2. Selected "Build your own template in the editor"
3. Loaded `azuredeploy.json` using the "Load file" button
4. Filled in the deployment parameters on the Basics form
5. Clicked "Review + create" → "Create"

**Equivalent Azure CLI command:**
```bash
az deployment group create \
  --resource-group 3mtt-azure-workspace-rg2 \
  --template-file azuredeploy.json \
  --parameters @azuredeploy.parameters.json
```

---

## 9. Troubleshooting Log

Multiple deployment errors were encountered and resolved during this project. This section documents the issues and their fixes — valuable reference for others facing similar challenges.

### Error 1 — InvalidDomainNameLabel
**Message:** `The domain name label 3mtt-ubuntu-vm-dns is invalid. It must conform to the regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$`  
**Cause:** DNS labels cannot start with a number. The VM name `3mtt-ubuntu-vm` began with `3`.  
**Fix:** Changed DNS label to use ARM's `uniqueString()` function prefixed with `mttvm` — ensuring it always starts with a letter.

### Error 2 — SkuNotAvailable (Standard_B1s)
**Message:** `Standard_B1s is currently not available in location 'eastus'`  
**Cause:** Microsoft has capacity restrictions on B1s VMs for new subscriptions in popular regions.  
**Fix:** Tried multiple regions (East US, East US 2, West US, Australia East) and VM sizes. Eventually found Standard_B2s_v2 available in West US 2.

### Error 3 — IPv4BasicSkuPublicIpCountLimitReached
**Message:** `Cannot create more than 0 IPv4 Basic SKU public IP addresses for this subscription`  
**Cause:** New Azure subscriptions cannot create Basic SKU Public IPs in certain regions.  
**Fix:** Changed Public IP SKU from `Basic` to `Standard` and allocation from `Dynamic` to `Static` (Standard SKU requires static allocation).

### Error 4 — CPU Architecture Mismatch
**Message:** `Standard_B2pts_v2 only supports CPU Architecture of Arm64, but an image with CPU Architecture x64 was given`  
**Cause:** The B2pts_v2 VM size uses ARM64 processors, incompatible with the standard Ubuntu x64 image.  
**Fix:** Changed VM size to an x64-compatible size.

### Error 5 — UseStandardSecurityType Feature Not Registered
**Message:** `The value 'Standard' is not available for property 'securityType' until the feature Microsoft.Compute/UseStandardSecurityType is registered`  
**Cause:** Explicitly setting `securityType: Standard` requires a feature flag not enabled on new subscriptions.  
**Fix:** Removed the `securityProfile` section entirely from the template, allowing Azure to assign the security type automatically.

### Error 6 — Region/SKU Combination Restrictions
**Cause:** Many VM sizes are restricted to specific regions on new subscriptions.  
**Fix:** Created a new resource group in West US 2 and used Standard_B2s_v2 which was confirmed available there.

---

## 10. Deployment Verification

Successful deployment was verified through:

1. **Azure Portal** — All 6 resources show "Succeeded" status in the resource group
2. **Serial Console** — Connected to the VM via Azure Serial Console and confirmed Ubuntu login prompt, verifying the VM is running and accessible
3. **Public IP** — VM was assigned a static public IP address confirming network connectivity

---

## 11. Post-Deployment Cleanup

To avoid unnecessary charges from the $200 Azure credit:
- VM was **stopped (deallocated)** immediately after verification
- Deallocated VMs do not incur compute charges
- Storage (OS disk) continues to incur minimal charges until deleted

---

## 12. Key Concepts Demonstrated

| Concept | Implementation |
|---------|---------------|
| **Infrastructure as Code** | All resources defined in JSON ARM template |
| **Declarative Configuration** | Described desired state; Azure determined how to achieve it |
| **Parameterization** | Separate parameters file for environment-specific values |
| **Resource Dependencies** | `dependsOn` ensures correct creation order |
| **Security Controls** | NSG with least-privilege inbound rules |
| **Repeatable Deployments** | Same template can deploy identical environments consistently |

---

*Report prepared as part of the 3MTT/Darey.io Cloud Computing Learning Path — Assignment 2.*
