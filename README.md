# Azure Cloud Computing Projects — 3MTT/Darey.io

**Student:** Kelechi Anuforo  
**Programme:** 3MTT (Three Million Technical Talent)  
**Platform:** Darey.io  
**Focus:** Microsoft Azure Cloud Computing  

---

## Repository Structure

```
azure-free-tier-setup/
├── README.md                          # This file
├── report/
│   └── azure_setup_report.md         # Assignment 1 report
├── screenshots/                       # Assignment 1 screenshots
└── assignment-2-arm-vm/
    ├── azuredeploy.json              # ARM template
    ├── azuredeploy.parameters.json   # Parameters file
    ├── deployment-report.md          # Assignment 2 report
    └── screenshots/                  # Assignment 2 screenshots
```

---

## Assignment 1 — Azure Free Tier Account Setup

**Status:** ✅ Completed  

### What Was Done
- Created a Microsoft Azure Pay-As-You-Go account with $200 free credits
- Navigated the Azure Portal and explored key services
- Created a Resource Group (`3mtt-azure-lab-rg`) in East US
- Explored Microsoft Entra ID and reviewed RBAC roles
- Deployed a Storage Account (`3mttlabstorage`) as a test IaaS/PaaS resource
- Configured a $0.50/month budget alert in Cost Management

### Key Concepts Covered
- Azure Portal navigation and dashboard customization
- Subscriptions and Resource Groups (cloud governance)
- Identity and Access Management (IAM) with RBAC
- Region selection and availability zones
- IaaS vs PaaS vs SaaS service models
- Shared Responsibility Model
- Cost management and free tier limits

---

## Assignment 2 — Azure VM Deployment via ARM Template

**Status:** ✅ Completed  

### What Was Done
- Created a JSON-based ARM template defining 5 Azure resources
- Configured compute, networking, and security resources declaratively
- Implemented parameterization via a separate parameters file
- Managed resource dependencies using `dependsOn` attributes
- Applied security controls via Network Security Group (NSG)
- Deployed a Ubuntu 22.04 LTS VM (Standard_B2s_v2) in West US 2
- Verified connectivity via Azure Serial Console
- Documented and resolved 6 deployment errors

### Resources Deployed
| Resource | Type | Location |
|----------|------|----------|
| myUbuntuVM | Virtual Machine | West US 2 |
| myUbuntuVM-nic | Network Interface | West US 2 |
| myUbuntuVM-nsg | Network Security Group | West US 2 |
| myUbuntuVM-publicip | Public IP Address | West US 2 |
| myUbuntuVM-vnet | Virtual Network | West US 2 |
| myUbuntuVM_OsDisk | Managed Disk | West US 2 |

### Azure CLI Deployment Command
```bash
az deployment group create \
  --resource-group 3mtt-azure-workspace-rg2 \
  --template-file azuredeploy.json \
  --parameters @azuredeploy.parameters.json
```

### Key Concepts Covered
- Infrastructure as Code (IaC) with ARM templates
- Declarative vs imperative infrastructure provisioning
- ARM template structure (parameters, variables, resources, outputs)
- Virtual Network, Subnet, NSG, NIC, Public IP configuration
- Resource dependency management
- Security best practices (NSG least-privilege rules)
- Deployment troubleshooting and error resolution

---

## Troubleshooting Highlights

This repository documents real-world deployment challenges encountered during the learning process, including:
- DNS label naming restrictions
- VM SKU capacity restrictions across regions
- Public IP SKU limitations on new subscriptions  
- CPU architecture mismatches
- Security type feature flag requirements

Full troubleshooting details are in the [Assignment 2 deployment report](assignment-2-arm-vm/deployment-report.md).

---

*Part of the 3MTT Federal Government of Nigeria digital skills initiative.*
