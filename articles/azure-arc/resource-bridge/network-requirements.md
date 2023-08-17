---
title: Azure Arc resource bridge (preview) network requirements
description: Learn about network requirements for Azure Arc resource bridge (preview) including URLs that must be allowlisted.
ms.topic: conceptual
ms.date: 01/30/2023
---

# Azure Arc resource bridge (preview) network requirements

This article describes the networking requirements for deploying Azure Arc resource bridge (preview) in your enterprise.

## General network requirements

[!INCLUDE [network-requirement-principles](../includes/network-requirement-principles.md)]

[!INCLUDE [network-requirements](includes/network-requirements.md)]

## Additional network requirements

In addition, resource bridge (preview) requires connectivity to the [Arc-enabled Kubernetes endpoints](../network-requirements-consolidated.md?tabs=azure-cloud).

> [!NOTE]
> The URLs listed here are required for Arc resource bridge only. Other Arc products (such as Arc-enabled VMware vSphere) may have additional required URLs. For details, see [Azure Arc network requirements](../network-requirements-consolidated.md).

## SSL proxy configuration

If using a proxy, Arc resource bridge must be configured for proxy so that it can connect to the Azure services. 

- To configure the Arc resource bridge with proxy, provide the proxy certificate file path during creation of the configuration files. 

- The format of the certificate file is *Base-64 encoded X.509 (.CER)*. 

- Only pass the single proxy certificate. If a certificate bundle is passed then the deployment will fail. 

- The proxy server endpoint can't be a .local domain. 

- The proxy server has to be reachable from all IPs within the IP address prefix, including the control plane and appliance VM IPs. 

There are only two certificates that should be relevant when deploying the Arc resource bridge behind an SSL proxy: 

- SSL certificate for your SSL proxy (so that the management machine and appliance VM trust your proxy FQDN and can establish an SSL connection to it)

- SSL certificate of the Microsoft download servers. This certificate must be trusted by your proxy server itself, as the proxy is the one establishing the final connection and needs to trust the endpoint. Non-Windows machines may not trust this second certificate by default, so you may need to ensure that it's trusted.

In order to deploy Arc resource bridge, images need to be downloaded to the management machine and then uploaded to the on-premises private cloud gallery. If your proxy server throttles download speed, this may impact your ability to download the required images (~3.5 GB) within the allotted time (90 min). 

## Exclusion list for no proxy

The following table contains the list of addresses that must be excluded by using the `-noProxy` parameter in the `createconfig` command.

|      **IP Address**       |    **Reason for exclusion**    |  
| ----------------------- | ------------------------------------ |
| localhost, 127.0.0.1  | Localhost traffic  |
| .svc | Internal Kubernetes service traffic (.svc) where _.svc_ represents a wildcard name. This is similar to saying \*.svc, but none is used in this schema. |
| 10.0.0.0/8 | private network address space |
| 172.16.0.0/12 |Private network address space - Kubernetes Service CIDR |
| 192.168.0.0/16 | Private network address space - Kubernetes Pod CIDR |
| .contoso.com | You may want to exempt your enterprise namespace (.contoso.com) from being directed through the proxy. To exclude all addresses in a domain, you must add the domain to the `noProxy` list. Use a leading period rather than a wildcard (\*) character. In the sample, the addresses `.contoso.com` excludes addresses `prefix1.contoso.com`, `prefix2.contoso.com`, and so on. |

The default value for `noProxy` is `localhost,127.0.0.1,.svc,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16`. While these default values will work for many networks, you may need to add more subnet ranges and/or names to the exemption list. For example, you may want to exempt your enterprise namespace (.contoso.com) from being directed through the proxy. You can achieve that by specifying the values in the `noProxy` list.

## Next steps

- Review the [Azure Arc resource bridge (preview) overview](overview.md) to understand more about requirements and technical details.
- Learn about [security configuration and considerations for Azure Arc resource bridge (preview)](security-overview.md).




