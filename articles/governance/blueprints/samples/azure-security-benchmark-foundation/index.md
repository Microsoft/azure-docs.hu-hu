---
title: Az Azure Security teljesítményteszt Foundation tervezetének áttekintése
description: Az Azure Security teljesítményteszt Foundation alaptervi mintájának áttekintése és architektúrája.
ms.date: 02/17/2020
ms.topic: sample
ms.openlocfilehash: ed497eff85e07b6a51939907bc751f3b40c99b30
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741871"
---
# <a name="overview-of-the-azure-security-benchmark-foundation-blueprint-sample"></a>Az Azure Security teljesítményteszt Foundation tervének áttekintése minta

Az Azure Security teljesítményteszt alaptervi mintája olyan alapszintű infrastruktúra-mintákat tartalmaz, amelyek segítségével biztonságos és megfelelő Azure-környezetet hozhat létre. A terv segít olyan felhőalapú architektúra üzembe helyezésében, amely az akkreditációs vagy megfelelőségi követelményekkel rendelkező forgatókönyvekhez nyújt megoldást. Ez az alapszintű tervrajzi minta az [Azure biztonsági teljesítményteszt minta tervezetének](../azure-security-benchmark.md)kiterjesztése. Üzembe helyezi és konfigurálja a hálózati határokat, a figyelést és az egyéb erőforrásokat az [Azure biztonsági teljesítménytesztben](../../../../security/benchmarks/index.yml)definiált szabályzatokkal és egyéb guardrails való igazodva.

## <a name="architecture"></a>Architektúra

A tervrajzi minta által létrehozott alapkörnyezet a [hub és a küllő modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)architektúráján alapul.
A terv üzembe helyez egy olyan központi virtuális hálózatot, amely közös és közös erőforrásokat, szolgáltatásokat és összetevőket tartalmaz, például az Azure-t, az átjárót és a tűzfalat a kapcsolat, a felügyelet és a Jump Box alhálózatok számára a további/opcionális felügyeleti, karbantartási, adminisztrációs és csatlakozási infrastruktúra üzemeltetéséhez. Egy vagy több küllős virtuális hálózat üzembe helyezése az alkalmazás-munkaterhelések, például a webes és az adatbázis-szolgáltatások üzemeltetésére történik. A küllős virtuális hálózatok zökkenőmentes és biztonságos kapcsolat érdekében az Azure Virtual Network-peering használatával csatlakoznak a hub virtuális hálózathoz. A további küllők a minta tervezetének újbóli hozzárendelésével vagy egy Azure-beli virtuális hálózat manuális létrehozásával és a hub virtuális hálózattal való társításával vehetők fel. A küllős virtuális hálózat (ok) hoz és az alhálózathoz (k) való minden külső kapcsolat úgy van konfigurálva, hogy a hub virtuális hálózatán, a tűzfalon, az átjárón és a felügyeleti Jump Box-on keresztül irányítsa a kapcsolatot.

:::image type="content" source="../../media/azure-security-benchmark-foundation/architecture.png" alt-text="Az Azure Security teljesítményteszt alaptervének minta architektúrájának diagramja" border="false":::

Ez a terv számos Azure-szolgáltatást üzembe helyez, hogy biztonságos, felügyelt, nagyvállalati használatra kész alapot biztosítson. A környezet összetevői:

- [Azure monitor naplókat](../../../../azure-monitor/logs/data-platform-logs.md) és egy Azure Storage-fiókot az erőforrás-naplók, a tevékenységek naplói, a metrikák és a hálózatok forgalmának biztosítása érdekében központi helyen tárolja az egyszerű lekérdezés, elemzés, archiválás és riasztás céljából.
- [Azure Security Center](../../../../security-center/security-center-introduction.md) (standard verzió) az Azure-erőforrások veszélyforrások elleni védelmének biztosításához.
- Az [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) a központban, amely támogatja az alhálózatokat a helyszíni hálózattal való kapcsolathoz, a bejövő és a kimenő adatforgalmat az internethez való csatlakozáshoz, valamint opcionális alhálózatokat a további felügyeleti vagy felügyeleti szolgáltatások üzembe helyezéséhez. A küllő Virtual Network alhálózatokat tartalmaz az alkalmazások számítási feladatainak üzemeltetéséhez. További alhálózatokat az üzembe helyezést követően lehet létrehozni a megfelelő forgatókönyvek támogatásához.
- [Azure Firewall](../../../../firewall/overview.md) az összes kimenő internetes forgalom átirányításához és a bejövő internetes forgalom engedélyezéséhez a Jump Box használatával. (Az alapértelmezett tűzfalszabályok blokkolják az összes internetes bejövő és kimenő forgalmat, a szabályokat pedig az üzembe helyezés után kell konfigurálni.)
- Minden alhálózathoz hozzárendelt [hálózati biztonsági csoportok](../../../../virtual-network/network-security-group-how-it-works.md) (NSG) (kivéve a szolgáltatás által birtokolt alhálózatokat, például az Azure Bastion, az átjáró és a Azure firewall), amelyek az összes internetes bejövő és kimenő forgalom blokkolására vannak konfigurálva.
- [Alkalmazás-biztonsági csoportok](../../../../virtual-network/application-security-groups.md) , amelyek lehetővé teszik az Azure-beli virtuális gépek csoportosítását a közös hálózati biztonsági házirendek alkalmazásához.
- [Útválasztási táblázatok](../../../../virtual-network/manage-route-table.md) az alhálózatok összes kimenő internetes forgalmának a tűzfalon keresztüli átirányításához. (Azure Firewall és a NSG szabályokat az üzembe helyezés után is konfigurálni kell a kapcsolat megnyitásához.)
- Az [azure Network Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) az Azure virtuális hálózatban lévő erőforrások metrikáinak monitorozására, diagnosztizálására és megtekintésére.
- [Azure DDoS Protection standard](../../../../ddos-protection/ddos-protection-overview.md) az Azure-erőforrások DDOS-támadások elleni védelemmel való ellátásához.
- Az [Azure Bastion](../../../../bastion/bastion-overview.md) zökkenőmentes és biztonságos kapcsolatot biztosít egy olyan virtuális géppel, amely nem igényel nyilvános IP-címet, ügynököt vagy speciális ügyfélszoftvert.
- Az [azure VPN Gateway](../../../../vpn-gateway/vpn-gateway-about-vpngateways.md) egy Azure-beli virtuális hálózat és egy helyszíni hely közötti titkosított adatforgalom engedélyezésére a nyilvános interneten keresztül.

> [!NOTE] 
> Az Azure Security teljesítményteszt Foundation alapvető architektúrát biztosít a számítási feladatokhoz. A fenti architektúra-diagram több fiktív erőforrást tartalmaz az alhálózatok lehetséges használatának bemutatásához. Ezen alapvető architektúrán továbbra is üzembe kell helyeznie a munkaterheléseket.

## <a name="next-steps"></a>Következő lépések

Áttekintette az Azure Security teljesítményteszt Foundation Blueprint minta áttekintését és architektúráját.

> [!div class="nextstepaction"]
> [Az Azure Security teljesítményteszt alapterve – a lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.