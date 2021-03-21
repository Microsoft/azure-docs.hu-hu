---
title: Az Azure Sentinel adatforrási sémájának referenciája
description: Ez a cikk az Azure Sentinel által támogatott Azure-és külső gyártótól származó adatforrás-sémákat sorolja fel, a hivatkozási dokumentációra mutató hivatkozásokkal.
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.custom: ''
ms.date: 01/14/2021
ms.openlocfilehash: 0cc1a083e1400b5a3167376e374af6c7bf7ab7c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698348"
---
# <a name="data-source-schema-reference"></a>Adatforrás sémájának referenciája

Ez a cikk a támogatott Azure-és külső adatforrások sémáit sorolja fel, és hivatkozásokat tartalmaz a rájuk vonatkozó dokumentációra.

## <a name="azure-data-sources"></a>Azure-beli adatforrások

| Típus                             | Adatforrás             | Log Analytics táblanév | Séma-hivatkozás |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [Azure AD-tevékenység jelentések bejelentkezési tulajdonságai](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | AuditLogs              | [Azure Monitor AuditLogs-hivatkozás](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Azure Monitor AzureActivity-hivatkozás](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | OfficeActivity         | Office 365 felügyeleti tevékenység API-sémái: <br>- [Általános séma ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Exchange-felügyeleti séma ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Exchange postaláda-séma](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [SharePoint alapsémája](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [SharePoint-fájl műveletei](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Azure Key Vault         | AzureDiagnostics       | [Azure Monitor AzureDiagnostics-hivatkozás](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Állomás**                             | Linux                  | Rendszernapló                 | [Syslog-dokumentáció Azure Monitor](/azure/azure-monitor/reference/tables/syslog) |
| **Hálózat**                          | IIS-naplók               | W3CIISLog              | [Azure Monitor W3CIISLog-hivatkozás](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Hálózat**                          | VMinsights             | VMConnection           | [Azure Monitor VMConnection-hivatkozás](/azure/azure-monitor/reference/tables/vmconnection) |
| **Hálózat**                          | Adatátviteli megoldás     | WireData               | [Azure Monitor WireData-hivatkozás](/azure/azure-monitor/reference/tables/wiredata) |
| **Hálózat**                          | NSG          | AzureNetworkAnalytics  | [Sémák és adatösszesítések Traffic Analytics](../network-watcher/traffic-analytics-schema.md) |
| | | | |

> [!NOTE]
> További információkért tekintse meg a teljes [Azure monitor adatreferenciát](/azure/azure-monitor/reference/).
>
## <a name="3rd-party-vendor-data-sources"></a>külső gyártótól származó adatforrások

A következő táblázat felsorolja a harmadik féltől származó gyártókat, valamint a syslog-vagy Common Event Format (CEF)-leképezési dokumentációt a különböző támogatott naplókhoz, amelyek CEF mező-hozzárendeléseket és mintavételi naplókat tartalmaznak az egyes kategóriákhoz.

| Típus |    Szállító |    Termék | Log Analytics táblanév | CEF-mező – leképezési hivatkozás  |
| ----- | ----- | ----- | ----- |----- |
| **Hálózat** | Palo Alto   | PÁN OPERÁCIÓS RENDSZER    | CommonSecurityLog |   [Pan-OS 9,0 Common Event Format integrációs útmutató](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) ( *CEF stílusú naplók* keresése) |
| **Hálózat** | Ellenőrzőpont  |ALL   | CommonSecurityLog | [Napló mezőinek leírása](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Hálózat** | FortiGate   | ALL   | CommonSecurityLog | [A naplózási séma szerkezete](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Hálózat** | Barracuda | Webalkalmazási tűzfal |  CommonSecurityLog   | [A syslog és más naplók konfigurálása](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Hálózat** | Cisco | ASA | CommonSecurityLog | [Cisco ASA sorozat syslog-üzenetei](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Hálózat** | Cisco | Tűzerő   | CommonSecurityLog | [Cisco tűzerő Threat Defense syslog-üzenetek](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.pdf)    |
| **Hálózat** | Cisco   | Esernyő  | Egyéni naplók táblázata  | [Naplózási formátumok és verziószámozás](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Hálózat**   | Cisco | Meraki    | CommonSecurityLog |   [Syslog-események típusai és a naplók mintái](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Hálózat**   | Zscaler | Nano streaming Service (NSS)|   CommonSecurityLog | Az [NSS-hírcsatornák](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (webes, TŰZFAL, DNS és bújtatási naplók) formázása |
| **Hálózat**   |F5 | BigIP LTM|    CommonSecurityLog|  [Az esemény üzenetei és a támadás típusai](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Hálózat** | F5  | BigIP ASM|    CommonSecurityLog|  [Alkalmazás biztonsági eseményeinek naplózása](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Hálózat** | Citrix  |Webalkalmazási tűzfal   | CommonSecurityLog|    [A Common Event Format (CEF) naplózásának támogatása az alkalmazás tűzfalán](https://support.citrix.com/article/CTX136146) <br>  [NetScaler 12,0 syslog-üzenet leírása](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Állomás** |Symantec | Symantec Endpoint Protection-kezelő (SEPM) | CommonSecurityLog|[A külső naplózási beállítások és a naplózási esemény súlyossági szintjei Endpoint Protection-kezelő](https://support.symantec.com/us/en/article.tech171741.html)|
|**Állomás** |Trend Micro |Mind |CommonSecurityLog | [Syslog-tartalom leképezése – CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

## <a name="next-steps"></a>Következő lépések

További információ az Azure Sentinel-összekötők, például a CEF, a syslog, a Direct, az Agent és az egyéni összekötők számára:

- [Adatforrások csatlakoztatása](connect-data-sources.md)

- [Azure Sentinel syslog, CEF és más külső felektől származó összekötők](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)