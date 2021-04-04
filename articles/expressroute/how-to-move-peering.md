---
title: 'Azure ExpressRoute: nyilvános Microsoft-partneri hálózat áthelyezése'
description: Ebből a cikkből megtudhatja, hogyan helyezheti át a nyilvános Microsoft-partneri kapcsolatot a ExpressRoute-on.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/12/2019
ms.author: duau
ms.openlocfilehash: 608d6c87442821e904fde16c6b75841fe792e3f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206288"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Váltáson nyilvános társviszony-létesítésről Microsoft-társviszony-létesítésre

Ebből a cikkből megtudhatja, hogyan helyezhet át egy nyilvános partneri konfigurációt a Microsoft-partnerek számára leállás nélkül. Az ExpressRoute támogatja a Microsoft-társviszony-létesítés útvonalszűrőkkel való használatát az Azure PaaS-szolgáltatások, például az Azure Storage és az Azure SQL Database esetén. A Microsoft PaaS- és SaaS-szolgáltatásokhoz való hozzáféréshez most csak egy útválasztási tartományra van szüksége. Útvonalszűrők segítségével szelektíven meghirdetheti a PaaS-szolgáltatások előtagjait a felhasználni kívánt Azure-régiókhoz.

Az Azure nyilvános társítása 1 NAT IP-címmel rendelkezik, amely az egyes BGP-munkamenetekhez van társítva. A Microsoft-partnerek lehetővé teszik saját NAT-foglalások konfigurálását, valamint az útválasztási szűrők használatát a szelektív előtaggal kapcsolatos hirdetményekhez. A nyilvános társítás egy egyirányú szolgáltatás, amellyel a kapcsolat mindig a WAN-ból Microsoft Azure szolgáltatásokhoz kezdeményezhető. Microsoft Azure szolgáltatás nem tud kapcsolatot létesíteni a hálózattal ezen az útválasztási tartományon keresztül.

Ha a nyilvános társítás engedélyezve van, csatlakozhat az összes Azure-szolgáltatáshoz. Nem engedélyezzük szelektíven olyan szolgáltatások kivételezését, amelyekhez útvonalakat hirdetünk. Míg a Microsoft-társítás egy kétirányú kapcsolat, ahol a Microsoft Azure szolgáltatással és a WAN-kapcsolattal is kezdeményezhető a kapcsolódás. További információ az útválasztási tartományokról és a társításról: [ExpressRoute-áramkörök és útválasztási tartományok](expressroute-circuit-peerings.md).

## <a name="before-you-begin"></a><a name="before"></a>Kezdés előtt

A Microsoft-partneri kapcsolathoz való kapcsolódáshoz be kell állítania és kezelnie kell a NAT-ot. A kapcsolati szolgáltató felügyelt szolgáltatásként állíthatja be és felügyelheti a NAT-t. Ha azt tervezi, hogy hozzáfér az Azure-beli és az Azure SaaS-szolgáltatásokhoz a Microsoft-partneri kapcsolaton, akkor fontos, hogy megfelelően méretezze a NAT IP-készletet. További információ a ExpressRoute NAT-ról: a [Microsoft-partnerek NAT-követelményei](expressroute-nat.md#nat-requirements-for-microsoft-peering). Ha az Azure ExpressRoute (Microsoft-társ) keresztül csatlakozik a Microsofthoz, a Microsofthoz több hivatkozása is van. Az egyik kapcsolat a meglévő internetkapcsolat, a másik pedig az ExpressRoute-on keresztüli kapcsolat. Előfordulhat, hogy a forgalom egy része az interneten keresztül jut el a Microsofthoz, de az ExpressRoute-on át tér vissza, vagy fordítva.

![Kétirányú kapcsolat](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> A Microsoft számára hirdetett NAT IP-készlet nem hirdethető meg az interneten. Ez megszakítja a más Microsoft-szolgáltatások kapcsolatait.

A Microsoft-társítás konfigurálása előtt tekintse meg a [több hálózati elérési úttal rendelkező aszimmetrikus útválasztást](./expressroute-asymmetric-routing.md) .

* Ha nyilvános társítást használ, és jelenleg IP-hálózati szabályok érvényesek az [Azure Storage](../storage/common/storage-network-security.md) vagy a [Azure SQL Database](../azure-sql/database/vnet-service-endpoint-rule-overview.md)ELÉRÉSére használt nyilvános IP-címekhez, meg kell győződnie arról, hogy a Microsoft-TÁRSÍTÁSSAL konfigurált NAT IP-címkészlet szerepel az Azure Storage-fiók vagy az Azure SQL-FIÓKHOZ tartozó nyilvános IP-címek listáján.<br>
* Ha a Microsoft-társat leállás nélkül szeretné áthelyezni, a jelen cikkben ismertetett lépéseket követve hozhatja meg.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. Microsoft-társak létrehozása

Ha nem hozta létre a Microsoft-társat, használja a következő cikkek bármelyikét a Microsoft-társak létrehozásához. Ha a kapcsolat szolgáltatója a felügyelt 3. rétegbeli szolgáltatásokat nyújtja, megkérheti a kapcsolat szolgáltatóját, hogy engedélyezze az áramkörhöz a Microsoft-partneri kapcsolatot.

Ha a 3. réteget felügyeli, a folytatás előtt a következő információk szükségesek:

* Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ebből az alhálózatból az első használható IP-címet hozzá kell rendelni az útválasztóhoz, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz.<br>
* Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ebből az alhálózatból az első használható IP-címet hozzá kell rendelni az útválasztóhoz, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz.<br>
* Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Az elsődleges és a másodlagos kapcsolatok esetében ugyanazt a VLAN-azonosítót kell használnia.<br>
* Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.<br>
* Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha előtagokat szeretne elküldeni, vesszővel tagolt listát is küldhet. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.<br>
* Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.

* Nem **kötelező** – ügyfél ASN: Ha olyan előtagokat hirdet meg, amelyek nem szerepelnek a társítási számként, megadhatja az as-számot, amelyre regisztrálva vannak.<br>
* **Opcionális** – egy MD5-kivonat, ha úgy dönt, hogy ezt használja.

A Microsoft-társak engedélyezésével kapcsolatos részletes utasítások a következő cikkekben találhatók:

* [Microsoft-társ létrehozása Azure Portal használatával](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Microsoft-társ létrehozása az Azure PowerShell-lel](expressroute-howto-routing-arm.md#msft)<br>
* [Microsoft-társ létrehozása az Azure CLI-vel](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. a Microsoft-társak ellenőrzése engedélyezve van

Győződjön meg arról, hogy a Microsoft-társítás engedélyezve van, és a meghirdetett nyilvános előtagok a konfigurált állapotban vannak.

* [Azure Portalra](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. útvonal-szűrő konfigurálása és csatolása az áramkörhöz

Alapértelmezés szerint az új Microsoft-társítások nem hirdetnek meg előtagokat addig, amíg egy útvonal-szűrő nincs csatolva az áramkörhöz. Útvonal-szűrő szabály létrehozásakor megadhatja azon Azure-régiókhoz tartozó szolgáltatási Közösségek listáját, amelyeket használni szeretne az Azure Pásti-szolgáltatásokhoz. Ez rugalmasságot biztosít az útvonalak szűrésére a követelmények szerint, ahogy az alábbi képernyőképen is látható:

![Nyilvános társak egyesítése](./media/how-to-move-peering/routefilter.jpg)

Adja meg az útvonal-szűrőket a következő cikkek bármelyikének használatával:

* [A Microsoft társközi útvonal-szűrőinek konfigurálása Azure Portal használatával](how-to-routefilter-portal.md)<br>
* [A Microsoft társközi útvonal-szűrőinek konfigurálása Azure PowerShell használatával](how-to-routefilter-powershell.md)<br>
* [A Microsoft társközi útvonal-szűrőinek konfigurálása az Azure CLI használatával](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. törölje a nyilvános társ-összevonást

Miután meggyőződött arról, hogy a Microsoft-társítás konfigurálva van, és a használni kívánt előtagok megfelelően vannak meghirdetve a Microsoft-társon, törölheti a nyilvános társ-összevonást. A nyilvános társak törléséhez használja a következő cikkek bármelyikét:

* [Azure-beli nyilvános társak törlése Azure PowerShell használatával](about-public-peering.md#powershell)
* [Nyilvános Azure-partnerek törlése a parancssori felületről](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. a társak megtekintése
  
Megtekintheti a Azure Portal összes ExpressRoute-áramkörét és-társát. További információ: a [Microsoft-partneri kapcsolat részleteinek megtekintése](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Következő lépések

A ExpressRoute kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.