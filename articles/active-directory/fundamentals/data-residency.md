---
title: Azure AD and data residency
description: Use residency data to manage access, achieve mobility scenarios, and secure your organization.
services: active-directory
author: janicericketts
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/21/2023
ms.author: jricketts
ms.reviewer: jricketts
ms.custom: "it-pro"
ms.collection:
---

# Azure Active Directory and data residency 

Azure AD is an Identity as a Service (IDaaS) solution that stores and manages identity and access data in the cloud. You can use the data to enable and manage access to cloud services, achieve mobility scenarios, and secure your organization. An instance of the Azure AD service, called a [tenant](../develop/developer-glossary.md#tenant), is an isolated set of directory object data that the customer provisions and owns.

## Core Store

The Core Store is made up of tenants stored in scale units, each of which contains multiple tenants. Update or retrieval data operations in the Azure AD Core Store relate to a single tenant, based on the user's security token, which achieves tenant isolation. Scale units are assigned to a geo-location. Each geo-location uses two or more Azure regions to store the data. In each Azure region, a scale unit data is replicated in the physical data centers for resiliency and performance.  

Learn more: [Azure Active Directory Core Store Scale Units](https://www.youtube.com/watch?v=OcKO44GtHh8)

Azure AD is available in the following clouds

* Public
* China
* US government

In the public cloud, you're prompted to select a location  at the time of tenant creation (for example, signing up for Office 365 or Azure, or creating more Azure AD instances through the Azure portal). Azure AD maps the selection to a geo-location and a single scale unit in it. Tenant location can't be changed after it's set.

The location selected during tenant creation will map to one of the following geo-locations:

* Australia
* Asia/Pacific
* Europe, Middle East, and Africa (EMEA)
* Japan
* North America
* Worldwide  

Azure AD handles Core Store data based on usability, performance, residency and/or other requirements based on geo-location. Azure AD replicates each tenant through its scale unit, across data centers, based on the following criteria: 

* Azure AD Core Store data, stored in data centers closest to the tenant-residency location, to reduce latency and provide fast user sign-in times
* Azure AD Core Store data stored in geographically isolated data centers to assure availability during unforeseen single-datacenter, catastrophic events
* Compliance with data residency, or other requirements, for specific customers and geo-locations

## Azure AD cloud solution models

Use the following table to see Azure AD cloud solution models based on infrastructure, data location, and operational sovereignty.

|Model|Locations|Data location|Operations personnel|Put a tenant in this model|
|---|---|---|---|---|
|Public geo located|North America, EMEA, Japan, Asia/Pacific|At rest, in the target location. Exceptions by service or feature|Operated by Microsoft. Microsoft datacenter personnel must pass a background check.|Create the tenant in the sign-up experience. Choose the location for data residency.|
|Public worldwide|Worldwide|All locations|Operated by Microsoft. Microsoft datacenter personnel must pass a background check.|Tenant creation available via official support channel and subject to Microsoft discretion.|
|Sovereign or national clouds|US government, China|At rest, in the target location. No exceptions.|Operated by a data custodian (1). Personnel are screened according to requirements.|Each national cloud instance has a sign-up experience.|

**Table references**:

(1) **Data custodians**: Data centers in the US government cloud are operated by Microsoft. In China, Azure AD is operated through a partnership with  [21Vianet](/microsoft-365/admin/services-in-china/services-in-china?redirectSourcePath=%252fen-us%252farticle%252fLearn-about-Office-365-operated-by-21Vianet-a8ab5061-3346-4da0-bb7c-5260822b53ae&view=o365-21vianet&viewFallbackFrom=o365-worldwide&preserve-view=true).  

Learn more: 

* [Customer data storage and processing for European customers in Azure AD](./data-storage-eu.md)
* Power BI: [Azure Active Directory – Where is your data located?](https://aka.ms/aaddatamap)
* [What is the Azure Active Directory architecture?](https://aka.ms/aadarch)
* [Find the Azure geography that meets your needs](https://azure.microsoft.com/overview/datacenters/how-to-choose/)
* [Microsoft Trust Center](https://www.microsoft.com/trustcenter/cloudservices/nationalcloud)

## Data residency across Azure AD components

Learn more: [Azure Active Directory, Product overview](https://www.microsoft.com/cloud-platform/azure-active-directory-features)

> [!NOTE]
> To understand service data location, such as Exchange Online, or Skype for Business, refer to the corresponding service documentation. 

### Azure AD components and data storage location

|Azure AD component|Description|Data storage location|
|---|---|---|
|Azure AD Authentication Service|This service is stateless. The data for authentication is in the Azure AD Core Store. It has no directory data. Azure AD Authentication Service generates log data in Azure storage, and in the data center where the service instance runs. When users attempt to authenticate using Azure AD, they're routed to an instance in the geographically nearest data center that is part of its Azure AD logical region. |In geo location|
|Azure AD Identity and Access Management (IAM)  Services|**User and management experiences**: The Azure AD management experience is stateless and has no directory data. It generates log and usage data stored in Azure Tables storage. The user experience is like the Azure portal. <br>**Identity management business logic and reporting services**: These services have locally cached data storage for groups and users. The services generate log and usage data that goes to Azure Tables storage, Azure SQL, and in Microsoft Elastic Search reporting services. |In geo location|
|Azure AD Multi-Factor Authentication (MFA)|For details about MFA-operations data storage and retention, see [Data residency and customer data for Azure AD multifactor authentication](../authentication/concept-mfa-data-residency.md). Azure AD MFA logs the User Principal Name (UPN), voice-call telephone numbers, and SMS challenges. For challenges to mobile app modes, the service logs the UPN and a unique device token. Data centers in the North America region store Azure AD MFA, and the logs it creates.|North America|
|Azure AD Domain Services|See regions where Azure AD Domain Services is published on [Products available by region](https://azure.microsoft.com/regions/services/). The service holds system metadata globally in Azure Tables, and it contains no personal data.|In geo location|
|Azure AD Connect Health|Azure AD Connect Health generates alerts and reports in Azure Tables storage and blob storage.|In geo location|
|Azure AD dynamic membership for groups, Azure AD self-service group management|Azure Tables storage holds dynamic membership rule definitions.|In geo location|
|Azure AD Application Proxy|Azure AD Application Proxy stores metadata about the tenant, connector machines, and configuration data in Azure SQL.|In geo location|
|Azure AD password writeback in Azure AD Connect|During initial configuration, Azure AD Connect generates an asymmetric keypair, using the Rivest–Shamir–Adleman (RSA) cryptosystem. It then sends the public key to the self-service password reset (SSPR) cloud service, which performs two operations: </br></br>1. Creates two Azure Service Bus relays for the Azure AD Connect on-premises service to communicate securely with the SSPR service </br> 2. Generates an Advanced Encryption Standard (AES) key, K1 </br></br> The Azure Service Bus relay locations, corresponding listener keys, and a copy of the AES key (K1) goes to Azure AD Connect in the response. Future communications between SSPR and Azure AD Connect occur over the new ServiceBus channel and are encrypted using SSL. </br> New password resets, submitted during operation, are encrypted with the RSA public key generated by the client during onboarding. The private key on the Azure AD Connect machine decrypts them, which prevents pipeline subsystems from accessing the plaintext password. </br> The AES key encrypts the message payload (encrypted passwords, more data, and metadata), which prevents malicious ServiceBus attackers from tampering with the payload, even with full access to the internal ServiceBus channel. </br> For password writeback, Azure AD Connect need keys and data: </br></br> - The AES key (K1) that encrypts the reset payload, or change requests from the SSPR service to Azure AD Connect, via the ServiceBus pipeline </br> - The private key, from the asymmetric key pair that decrypts the passwords, in reset or change request payloads </br> - The ServiceBus listener keys </br></br> The AES key (K1) and the asymmetric keypair rotate a minimum of every 180 days, a duration you can change during certain onboarding or offboarding configuration events. An example is a customer disables and re-enables password writeback, which might occur during component upgrade during service and maintenance. </br> The writeback keys and data stored in the Azure AD Connect database are encrypted by data protection application programming interfaces (DPAPI) (CALG_AES_256). The result is the master ADSync encryption key stored in the Windows Credential Vault in the context of the ADSync on-premises service account. The Windows Credential Vault supplies automatic secret re-encryption as the password for the service account changes. To reset the service account password invalidates secrets in the Windows Credential Vault for the service account. Manual changes to a new service account might invalidate the stored secrets.</br> By default, the ADSync service runs in the context of a virtual service account. The account might be customized during installation to a least-privileged domain service account, a managed service account (MSA), or a group managed service account (gMSA). While virtual and managed service accounts have automatic password rotation, customers manage password rotation for a custom provisioned domain account.  As noted, to reset the password causes loss of stored secrets. |In geo location|
|Azure AD Device Registration Service |Azure AD Device Registration Service has computer and device lifecycle management in the directory, which enable scenarios such as device-state Conditional Access, and mobile device management.|In geo location|
|Azure AD provisioning|Azure AD provisioning creates, removes, and updates users in systems, such as software as service (SaaS) applications. It manages user creation in Azure AD and on-premises AD from cloud HR sources, like Workday. The service stores its configuration in an Azure Cosmos DB, which stores the group membership data for the user directory it keeps. Cosmos DB replicates the database to multiple datacenters in the same region as the tenant, which isolates the data, according to the Azure AD cloud solution model. Replication creates high availability and multiple reading and writing endpoints. Cosmos DB has encryption on the database information, and the encryption keys are stored in the secrets storage for Microsoft.|In geo location|
|Azure AD business-to-business (B2B) collaboration|Azure AD B2B collaboration has no directory data. Users and other directory objects in a B2B relationship, with another tenant, result in user data copied in other tenants, which might have data residency implications.|In geo location|
|Azure AD Identity Protection|Azure AD Identity Protection uses real-time user log-in data, with multiple signals from company and industry sources, to feed its machine-learning systems that detect anomalous logins. Personal data is scrubbed from real-time log-in data before it's passed to the machine learning system. The remaining log-in data identifies potentially risky usernames and logins. After analysis, the data goes to Microsoft reporting systems. Risky logins and usernames appear in reporting for Administrators.|In geo location|
|Azure AD managed identities for Azure resources|Azure AD managed identities for Azure resources with managed identities systems can authenticate to Azure services, without storing credentials. Rather than use username and password, managed identities authenticate to Azure services with certificates. The service writes certificates it issues in Azure Cosmos DB in the East US region, which fail over to another region, as needed. Azure Cosmos DB geo-redundancy occurs by global data replication. Database replication puts a read-only copy in each region that Azure AD managed identities runs. To learn more, see [Azure services that can use managed identities to access other services](../managed-identities-azure-resources/managed-identities-status.md). Microsoft isolates each Cosmos DB instance in an Azure AD cloud solution model. </br> The resource provider, such as the virtual machine (VM) host, stores the certificate for authentication, and identity flows, with other Azure services. The service stores its master key to access Azure Cosmos DB in a datacenter secrets management service. Azure Key Vault stores the master encryption keys.|In geo location|
|Azure Active Directory B2C |[Azure AD B2C](../../active-directory-b2c/data-residency.md) is an identity management service to customize and manage how customers sign up, sign in, and manage their profiles when using applications. B2C uses the Core Store to keep user identity information. The Core Store database follows known storage, replication, deletion, and data-residency rules. B2C uses an Azure Cosmos DB system to store service policies and secrets. Cosmos DB has encryption and replication services on database information. Its encryption key is stored in the secrets storage for Microsoft. Microsoft isolates Cosmos DB instances in an Azure AD cloud solution model.|Customer-selectable geo location|

## Related resources

For more information on data residency in Microsoft Cloud offerings, see the following articles:

* [Azure Active Directory – Where is your data located?](https://aka.ms/aaddatamap)
* [Data Residency in Azure | Microsoft Azure](https://azure.microsoft.com/explore/global-infrastructure/data-residency/#overview)
* [Microsoft 365 data locations - Microsoft 365 Enterprise](/microsoft-365/enterprise/o365-data-locations?view=o365-worldwide&preserve-view=true)
* [Microsoft Privacy - Where is Your Data Located?](https://www.microsoft.com/trust-center/privacy/data-location?rtc=1)
* Download PDF: [Privacy considerations in the cloud](https://go.microsoft.com/fwlink/p/?LinkID=2051117&clcid=0x409&culture=en-us&country=US)

## Next steps

* [Azure Active Directory and data residency](data-residency.md) (You're here)

* [Data operational considerations](data-operational-considerations.md)
* [Data protection considerations](data-protection-considerations.md)
