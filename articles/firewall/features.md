---
title: Az Azure Firewall funkciói
description: Tudnivalók a Azure Firewall szolgáltatásairól
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: victorh
ms.openlocfilehash: 69eaf3ca60378afd810d712d85ea7ef732e41e3e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788230"
---
# <a name="azure-firewall-features"></a>Az Azure Firewall funkciói

A [Azure Firewall](overview.md) felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely védelmet nyújt az Azure-Virtual Network erőforrásainak.

![Tűzfal áttekintése](media/overview/firewall-threat.png)

Azure Firewall a következő funkciókat tartalmazza:

- [Beépített magas rendelkezésre állás](#built-in-high-availability)
- [Rendelkezésre állási zónák](#availability-zones)
- [Korlátlan Felhőbeli méretezhetőség](#unrestricted-cloud-scalability)
- [Alkalmazások teljes tartománynevére vonatkozó szűrési szabályok](#application-fqdn-filtering-rules)
- [Hálózati forgalomra vonatkozó szűrési szabályok](#network-traffic-filtering-rules)
- [FQDN-címkék](#fqdn-tags)
- [Szolgáltatáscímkék](#service-tags)
- [Fenyegetésészlelési intelligencia](#threat-intelligence)
- [Kimenő SNAT-támogatás](#outbound-snat-support)
- [Bejövő DNAT-támogatás](#inbound-dnat-support)
- [Több nyilvános IP-cím](#multiple-public-ip-addresses)
- [Azure Monitor naplózás](#azure-monitor-logging)
- [Alagúthasználat kényszerítése](#forced-tunneling)
- [Tanúsítványok](#certifications)

## <a name="built-in-high-availability"></a>Beépített magas rendelkezésre állás

A magas rendelkezésre állás beépített, így nincs szükség további terheléselosztó megadására, és nincs szükség a konfigurálásra.

## <a name="availability-zones"></a>Rendelkezésre állási zónák

A Azure Firewall az üzembe helyezés során konfigurálható úgy, hogy a nagyobb rendelkezésre állás érdekében több Availability Zones is kiterjedhet. A Availability Zones a rendelkezésre állás 99,99%-os üzemidőre növekszik. További információ: Azure Firewall [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Ha két vagy több Availability Zones van kiválasztva, a 99,99%-os rendelkezésre állási SLA-t ajánljuk.

A szolgáltatáshoz a 99,95%-os SLA-t használó szabványos szolgáltatási szabványnak megfelelően a Azure Firewallt is hozzárendelheti egy adott zónához.

Egy rendelkezésre állási zónában üzembe helyezett tűzfal esetében nincs további díj. A Availability Zoneshoz társított bejövő és kimenő adatátvitelek esetében azonban további költségek is rendelkezésre állnak. További információ: a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/).

Azure Firewall Availability Zones a Availability Zones támogató régiókban érhetők el. További információ: [Availability Zonest támogató régiók az Azure-ban](../availability-zones/az-region.md)

> [!NOTE]
> Availability Zones csak az üzembe helyezés során állítható be. Meglévő tűzfal nem konfigurálható úgy, hogy tartalmazza a Availability Zones.

További információ a Availability Zonesről: [régiók és Availability Zones az Azure-ban](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Korlátlan felhőalapú skálázhatóság

Az Azure Firewall akármeddig felskálázható a változó hálózati forgalom kezeléséhez, így a költségvetést nem szükséges a csúcsforgalomhoz igazítania.

## <a name="application-fqdn-filtering-rules"></a>Alkalmazások teljes tartománynevére vonatkozó szűrési szabályok

A kimenő HTTP/S forgalom vagy az Azure SQL-forgalom a teljes tartománynevek (FQDN) egy adott listájára korlátozható, beleértve a Wild kártyákat is. Ez a funkció nem igényli a TLS-megszakítást.

## <a name="network-traffic-filtering-rules"></a>Hálózati forgalomra vonatkozó szűrési szabályok

A hálózati szűrési szabályok a forrás  és a cél IP-cím, a port és a protokoll alapján központilag hozhatók *létre.* Az Azure Firewall teljes mértékben állapotalapú, így képes megkülönböztetni különböző típusú kapcsolatok érvényes csomagjait. A szabályok több előfizetésen és virtuális hálózaton érvényesíthetők és naplózhatók.

## <a name="fqdn-tags"></a>FQDN-címkék

A [teljes tartománynevek](fqdn-tags.md) lehetővé teszik, hogy a tűzfalon keresztül a jól ismert Azure-szolgáltatás hálózati forgalmát engedélyezze. Tegyük fel például, hogy engedélyezni kívánja a Windows Update hálózati forgalmát a tűzfalon keresztül. Létrehozhat egy alkalmazásszabályt, és hozzáadhatja a Windows Update címkéjét. A Windows Update hálózati forgalma ezután akadálytalanul áthaladhat a tűzfalon.

## <a name="service-tags"></a>Szolgáltatáscímkék

A [szolgáltatási címke](service-tags.md) az IP-címek egy csoportját jelöli, így könnyebben csökkenthető a biztonsági szabályok létrehozásának bonyolultsága. Nem hozhat létre saját szolgáltatási címkét, és nem adhatja meg, hogy mely IP-címek szerepeljenek a címkén belül. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket.

## <a name="threat-intelligence"></a>Fenyegetésészlelési intelligencia

A [fenyegetésekkel kapcsolatos intelligencia](threat-intel.md)-alapú szűrés engedélyezhető a tűzfal számára, hogy riasztást kapjon, és megtagadja a forgalmat az ismert kártékony IP-címekre és tartományokra. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

## <a name="outbound-snat-support"></a>Kimenő SNAT-támogatás

A rendszer a kimenő virtuális hálózati forgalomhoz tartozó minden IP-címet lefordít az Azure Firewall nyilvános IP-címére (forráshálózati címfordítás, SNAT). Azonosíthatja és engedélyezheti a virtuális hálózatból a távoli internetes célhelyekre irányuló forgalmat. A Azure Firewall nem SNAT, ha a cél IP-cím egy [IANA RFC 1918-es](https://tools.ietf.org/html/rfc1918)magánhálózati IP-címtartomány. 

Ha a szervezete nyilvános IP-címtartományt használ a magánhálózatok számára, Azure Firewall a SNAT a AzureFirewallSubnet-ben lévő egyik tűzfal magánhálózati IP-címére irányítja át a forgalmat. A Azure Firewall konfigurálhatja úgy, hogy **ne** SNAT a nyilvános IP-címtartományt. További információ: [Azure Firewall SNAT magánhálózati IP-címtartományok](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Bejövő DNAT-támogatás

A tűzfal nyilvános IP-címére irányuló bejövő internetes hálózati forgalom le van fordítva (célként megadott hálózati címfordítás), és a virtuális hálózatok magánhálózati IP-címeire szűrve.

## <a name="multiple-public-ip-addresses"></a>Több nyilvános IP-cím

A tűzfallal [több nyilvános IP-címet](deploy-multi-public-ip-powershell.md) is hozzárendelhet (legfeljebb 250).

Ez a következő forgatókönyveket teszi lehetővé:

- **DNAT** – a háttér-kiszolgálókra több szabványos port-példányt is lefordíthat. Ha például két nyilvános IP-címmel rendelkezik, akkor mindkét IP-címhez lefordíthatja a 3389-es (RDP) TCP-portot.
- **SNAT** – további portok érhetők el a kimenő SNAT-kapcsolatokhoz, ami csökkenti a SNAT-portok kimerülésének lehetséges lehetőségét. Ekkor Azure Firewall véletlenszerűen kiválasztja a forrás nyilvános IP-címét, amelyet a rendszer a kapcsolódáshoz használ. Ha a hálózaton bármilyen lefelé irányuló szűrés van érvényben, engedélyeznie kell a tűzfalhoz társított összes nyilvános IP-címet. Érdemes lehet egy [nyilvános IP-cím előtagot](../virtual-network/public-ip-address-prefix.md) használni a konfiguráció egyszerűsítéséhez.

## <a name="azure-monitor-logging"></a>Azure Monitor-naplózás

A rendszer minden eseményt integrál a Azure Monitorba, így lehetővé teszi a naplók archiválását egy Storage-fiókba, az események továbbítását az Event hub-ba, vagy elküldheti őket Azure Monitor naplókba. Azure Monitor naplózási minták esetében lásd: [Azure monitor naplók a Azure Firewall](./firewall-workbook.md).

További információ: [oktatóanyag: Azure Firewall-naplók és-metrikák figyelése](./firewall-diagnostics.md). 

Azure Firewall munkafüzet rugalmas vásznon biztosít Azure Firewall adatelemzéshez. A szolgáltatással gazdag vizuális jelentéseket hozhat létre a Azure Portalon belül. További információ: [naplók figyelése Azure Firewall munkafüzet használatával](firewall-workbook.md).

## <a name="forced-tunneling"></a>Alagúthasználat kényszerítése

A Azure Firewall konfigurálható úgy, hogy az összes internetes forgalmat a következő ugrásra irányítsa, ahelyett, hogy közvetlenül az internethez kellene jutnia. Előfordulhat például, hogy egy helyszíni peremhálózati tűzfallal vagy más hálózati virtuális berendezéssel (NVA) szeretné feldolgozni a hálózati forgalmat az internetre való továbbítás előtt. További információ: [Azure Firewall kényszerített bújtatás](forced-tunneling.md).

## <a name="certifications"></a>Tanúsítványok

A Azure Firewall a Payment Card Industry (PCI), a Service Organization Controls (SOC), a International Organization for Standardization (ISO) és a ICSA Labs megfelelője. További információ: [Azure Firewall megfelelőségi tanúsítványok](compliance-certifications.md).

## <a name="next-steps"></a>További lépések

- [Az Azure Firewall szabályfeldolgozási logikája](rule-processing.md)