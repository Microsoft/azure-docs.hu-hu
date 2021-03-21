---
title: Azure Firewall fenyegetések felderítésére alapuló szűrés
description: Engedélyezheti a veszélyforrás-felderítésen alapuló szűrést a tűzfalon az ismert kártékony IP-címekről és tartományokból származó vagy ezek felé irányuló adatforgalommal kapcsolatos riasztáshoz, illetve a forgalom letiltásához.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/12/2020
ms.author: victorh
ms.openlocfilehash: 77e36d5ab98f1177b5a0b89ee10b3e6bd22560c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791459"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Azure Firewall fenyegetés intelligencia-alapú szűrés

Engedélyezheti a veszélyforrás-felderítésen alapuló szűrést a tűzfalon az ismert kártékony IP-címekről és tartományokból származó vagy ezek felé irányuló adatforgalommal kapcsolatos riasztáshoz, illetve a forgalom letiltásához. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája. A [intelligens biztonsági gráf](https://www.microsoft.com/security/operations/intelligence) a Microsoft fenyegetésekkel kapcsolatos intelligenciát, és több szolgáltatás, például Azure Security Center használatát is használja.<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="Tűzfal-veszélyforrások felderítése" border="false":::

Ha engedélyezte a fenyegetést jelentő intelligencia-alapú szűrést, a rendszer feldolgozza a társított szabályokat a NAT-szabályok, a hálózati szabályok vagy az alkalmazási szabályok előtt.

Dönthet úgy, hogy csak a riasztást naplózza egy szabály indításakor, vagy választhatja a riasztás és a Megtagadás módot is.

Alapértelmezés szerint a fenyegetési intelligencia-alapú szűrés riasztási módban van engedélyezve. Ezt a funkciót nem lehet kikapcsolni, vagy módosítani a módot, amíg a portál felülete elérhetővé nem válik a régiójában.

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="Fenyegetésekkel kapcsolatos intelligencia-alapú szűrési portál felülete":::

## <a name="logs"></a>Naplók

A következő naplózási részlet egy aktivált szabályt mutat be:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Tesztelés

- **Kimenő tesztelés** – a kimenő forgalmi riasztásoknak ritka előfordulásnak kell lenniük, mivel ez azt jelenti, hogy a környezet biztonsága sérült. A kimenő riasztások tesztelésének segítése érdekében a rendszer létrehoz egy teljes tartománynevet, amely riasztást indít el. A kimenő tesztek **testmaliciousdomain.eastus.cloudapp.Azure.com** használata.

- **Bejövő tesztelés** – a rendszer a bejövő forgalomra vonatkozó riasztásokat jelenít meg, ha a TŰZFALon DNAT-szabályok vannak konfigurálva. Ez akkor is igaz, ha csak bizonyos források engedélyezettek a DNAT-szabályban, és a forgalom egyébként meg van tagadva. Azure Firewall az összes ismert porton nem jeleníti meg a riasztásokat; csak olyan lapolvasók esetében, amelyekről ismert, hogy rosszindulatú tevékenységet is folytatnak.

## <a name="next-steps"></a>Következő lépések

- Lásd: [Azure Firewall log Analytics minták](./firewall-workbook.md)
- Megtudhatja, hogyan [helyezhet üzembe és konfigurálhat egy Azure Firewall](tutorial-firewall-deploy-portal.md)
- A [Microsoft biztonsági intelligencia jelentésének](https://www.microsoft.com/en-us/security/operations/security-intelligence-report) áttekintése