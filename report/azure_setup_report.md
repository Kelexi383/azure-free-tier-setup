# Azure Free Tier Account Setup — Summary Report
**Programme:** 3MTT (Three Million Technical Talent) Learning Programme  
**Platform:** Darey.io  
**Submitted by:** Kelechi Anuforo  
**Date:** June 4, 2026  

---

## 1. Overview

This report documents the completion of the Azure Free Tier Account Setup project. The objective was to establish a functional Microsoft Azure environment, explore the portal, configure governance and identity settings, deploy a cloud resource, and set up cost monitoring — all as a foundational introduction to cloud computing.

---

## 2. Account & Subscription

A Microsoft Azure account was successfully created using the Pay-As-You-Go offer (MS-AZR-0003P) under the Outlook account `kelechifavour100@outlook.com`. The account is active under **Azure subscription 1**, with **$200.00 in free credits** valid until July 4, 2026.

---

## 3. Resource Group

A Resource Group was created to serve as a logical container for all lab resources:

| Field | Value |
|-------|-------|
| **Resource Group Name** | 3mtt-azure-lab-rg |
| **Subscription** | Azure subscription 1 |
| **Region** | East US |
| **Tags** | Project: 3MTT-Darey |

---

## 4. Identity & Access Management (IAM)

Microsoft Entra ID (formerly Azure Active Directory) was explored, including:

- **Users** — the account owner (`kelechifavour100@outlook.com`) was identified as the sole user in the directory.
- **Roles and Administrators** — the built-in RBAC roles were reviewed, including:
  - **Owner** — full access including the ability to assign roles to others
  - **Contributor** — can create and manage all resources but cannot assign roles
  - **Reader** — read-only access to all resources

RBAC enables fine-grained access control, ensuring that only authorised identities can perform specific actions on Azure resources.

---

## 5. Region Selection

**Selected Region:** East US (eastus)

### Justification
Although **South Africa North (Johannesburg)** is the geographically closest Azure region to Nigeria (~2,500 km), **East US** was selected for this learning exercise for the following reasons:

- East US offers the **widest availability of Azure services and features**, ensuring all lab tasks can be completed without regional service restrictions.
- Several Azure services and capabilities are available in US regions before rolling out to other regions.
- For a **production deployment** serving Nigerian end users, South Africa North would be the optimal choice to minimise latency and comply with data residency considerations.

### Azure Regions & Availability Zones
Azure organises its global infrastructure into **regions** (geographic areas containing one or more data centres) and **availability zones** (physically separate data centres within a region, each with independent power, cooling, and networking). East US contains multiple availability zones, providing high availability and fault tolerance for production workloads.

---

## 6. Resource Deployment — Storage Account (IaaS/PaaS)

A **Storage Account** was deployed as a test resource to understand the Azure provisioning workflow:

| Field | Value |
|-------|-------|
| **Resource Name** | 3mttlabstorage |
| **Resource Type** | Storage Account (StorageV2) |
| **Resource Group** | 3mtt-azure-lab-rg |
| **Location** | East US |
| **Performance** | Standard |
| **Replication** | Locally Redundant Storage (LRS) |
| **Provisioning State** | Succeeded |
| **Created** | June 4, 2026 |

### Shared Responsibility Model

The **Shared Responsibility Model** defines the division of security and operational responsibilities between Microsoft (the cloud provider) and the customer (the user). For the Storage Account deployed in this project:

| Responsibility | Microsoft | Customer (Me) |
|---------------|-----------|---------------|
| Physical data centre security | ✅ | |
| Network infrastructure & hardware | ✅ | |
| Hypervisor and host OS | ✅ | |
| Storage service availability & uptime | ✅ | |
| Data encryption at rest (default) | ✅ | |
| Access control (IAM/RBAC) | | ✅ |
| Data stored inside the storage account | | ✅ |
| Account credentials & key management | | ✅ |
| Configuring network access rules | | ✅ |
| Compliance with data regulations | | ✅ |

In summary, Microsoft secures the underlying infrastructure, while the customer is responsible for securing access to data, managing credentials, and ensuring appropriate configuration of the deployed resource.

---

## 7. Cost Management Configuration

A budget alert was configured in the **Cost Management + Billing** section to ensure spending remains within free tier limits:

| Field | Value |
|-------|-------|
| **Budget Name** | kelechi-3mtt-darey |
| **Scope** | Kelechi Anuforo (Billing account) |
| **Budget Amount** | $0.50/month |
| **Reset Period** | Monthly |
| **Expiration Date** | May 31, 2028 |
| **Current Spend** | $0.00 (0.00%) |

An email alert is configured to notify the account owner when spending approaches the budget threshold, providing proactive cost visibility.

---

## 8. Key Concepts Learned

### IaaS vs PaaS vs SaaS
| Model | Definition | Azure Example |
|-------|-----------|---------------|
| **IaaS** (Infrastructure as a Service) | You manage the OS, runtime, and apps; Microsoft manages hardware | Azure Virtual Machines |
| **PaaS** (Platform as a Service) | Microsoft manages the OS and runtime; you manage only your app and data | Azure App Service |
| **SaaS** (Software as a Service) | Microsoft manages everything; you just use the application | Microsoft 365 |

The Storage Account deployed in this project is a **PaaS** resource — Microsoft manages all underlying infrastructure and the storage service itself, while the customer manages data and access configuration.

---

*Report prepared as part of the 3MTT/Darey.io Cloud Computing Learning Path.*
