---
title: Riasztások továbbítása Azure Security Centerról a biztonsági információkra és az Event Management (SIEM) rendszerekre és más figyelési megoldásokra
description: Ismerje meg, hogy miként továbbíthatja biztonsági riasztásait az Azure Sentinel, a harmadik féltől származó SIEM, a SOAR vagy a ITSM-megoldásokba
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 3ddc385b9d489e0c2ab4abf35a6ade011970342b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572959"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Riasztások továbbítása SIEM, SOAR vagy IT Service Management megoldásba

A Azure Security Center a biztonsági riasztásokat a legnépszerűbb biztonsági információkra és esemény-kezelésre (SIEM), a biztonsági összehangolás automatizált válaszára (SOAR) és az IT Service Management-(ITSM-) megoldásokra is továbbíthatja.

Vannak olyan Azure-natív eszközök, amelyekkel biztosíthatja, hogy a jelenleg használatban lévő legnépszerűbb megoldásokban megtekintse a riasztási adatait, többek között:

- **Azure Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **Az IBM QRadar**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Stream-riasztások az Azure Sentinelhez 

Security Center natív módon integrálható az Azure Sentinel, az Azure felhőalapú SIEM és SOAR megoldásával. 

[További információ az Azure sentinelről](../sentinel/overview.md).

### <a name="azure-sentinels-connectors-for-security-center"></a>Az Azure Sentinel összekötői Security Center

Az Azure Sentinel beépített összekötőket tartalmaz a Azure Security Center az előfizetés és a bérlő szintjén:

- [Stream-riasztások az Azure Sentinelhez az előfizetés szintjén](../sentinel/connect-azure-security-center.md)
- [A bérlő összes előfizetésének összekötése az Azure Sentinel szolgáltatással](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>Az összes napló betöltésének beállítása az Azure Sentinelbe 

Egy másik alternatíva Security Center riasztások kivizsgálása az Azure Sentinelben a naplók továbbítása az Azure Sentinelbe:
    - [A Windows biztonsági eseményeinek csatlakoztatása](../sentinel/connect-windows-security-events.md)
    - [Adatok gyűjtése Linux-alapú forrásokból a syslog használatával](../sentinel/connect-syslog.md)
    - [Adatok összekapcsolása az Azure-beli tevékenység naplójából](../sentinel/connect-azure-activity.md)

> [!TIP]
> Az Azure Sentinel számlázása az Azure Sentinelben az elemzéshez és az Azure Monitor Log Analytics munkaterületen tárolt adatmennyiség alapján történik. Az Azure Sentinel rugalmas és kiszámítható díjszabási modellt kínál. [További információ az Azure Sentinel díjszabási oldalán található](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Stream-riasztások Microsoft Graph biztonsági API-val

A Security Center a Microsoft Graph biztonsági API-val való beépített integrációt. Nincs szükség konfigurációra, és nincsenek további költségek. 

Ezzel az API-val a **teljes bérlő** (és számos más Microsoft biztonsági termékből származó adatok) riasztásait továbbíthatja harmadik féltől származó Siem és más népszerű platformokra:

- **Splunk Enterprise és splunk Cloud**  -  [A Splunk Microsoft Graph biztonsági API-Add-On használata](https://splunkbase.splunk.com/app/4564/) 
- **Power bi**  -  [Kapcsolódjon a Microsoft Graph biztonsági API-hoz Power bi Desktop](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow**  -  [Kövesse a Microsoft Graph Security API-alkalmazás ServiceNow-tárolóból történő telepítésének és konfigurálásának utasításait](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar**  -  [IBM eszköz-támogatási modulja Azure Security Center Microsoft Graph API-n keresztül](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto hálózatok**, **Anomali**, **kilátó**, **inspark** és több [Microsoft Graph biztonsági API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[További információ a Microsoft Graph biztonsági API-ról](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Stream-riasztások Azure Monitor 

Riasztások továbbítása a **ArcSight**, a **splunk**, a **SumoLogic**, a syslog-kiszolgálókra, a **LogRhythm**, a Logz.IO Cloud betekintési **platformra** és más figyelési megoldásokra. Security Center összekapcsolhatók az Azure monitorral az Azure Event Hubs használatával:

1. Engedélyezze a [folyamatos exportálást](continuous-export.md) a stream Security Center a riasztásokat egy dedikált Azure Event hubhoz az előfizetés szintjén. 
    > [!TIP]
    > Ha ezt a felügyeleti csoport szintjén szeretné elvégezni Azure Policy használatával, tekintse meg a [folyamatos exportálás automatizálási konfigurációinak létrehozása a skálán](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies) című témakört.

1. [A Azure monitor beépített összekötői segítségével csatlakoztathatja az Azure Event hub-t az előnyben részesített megoldáshoz](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

1. Szükség esetén a nyers naplókat továbbíthatja az Azure Event hub-ba, és csatlakozhat a kívánt megoldáshoz. További információ az [elérhető figyelési](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available)információkkal kapcsolatban.

> [!TIP]
> Az exportált adattípusok esemény-sémáinak megtekintéséhez keresse fel az [Event hub esemény-sémáit](https://aka.ms/ASCAutomationSchemas).


## <a name="next-steps"></a>Következő lépések

Ez az oldal azt ismerteti, hogyan biztosítható, hogy a Azure Security Center riasztási adatai elérhetők legyenek a SIEM, a SOAR vagy a ITSM eszközben. Kapcsolódó anyagok esetében lásd:

- [Mi az Azure Sentinel?](../sentinel/overview.md)
- [Riasztások érvényesítése Azure Security Centerban](security-center-alert-validation.md) – ellenőrizze, hogy a riasztások megfelelően vannak-e konfigurálva
- [Security Center-adatfeldolgozás folyamatos exportálása](continuous-export.md)