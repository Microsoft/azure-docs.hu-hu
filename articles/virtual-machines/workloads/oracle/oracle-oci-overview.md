---
title: Microsoft Azure integrálása az Oracle Cloud Infrastructure szolgáltatással | Microsoft Docs
description: Ismerje meg azokat a megoldásokat, amelyek a Microsoft Azureon futó Oracle-alkalmazásokat integrálják az Oracle Cloud Infrastructure (OCI) adatbázisaival.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 06/01/2020
ms.author: kegorman
ms.openlocfilehash: b27db94775e2ba8275dbdab4bf5bd61cc0fdf5c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666864"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Oracle Application Solutions Microsoft Azure és Oracle Cloud Infrastructure integrációja

A Microsoft és az Oracle partneri kapcsolatban állt a kis késleltetésű, nagy átviteli sebességű, Felhőbeli kapcsolattal, így kihasználhatja mindkét felhő közül a legjobbat. 

Ezzel a Felhőbeli kapcsolattal particionálhat többrétegű alkalmazást az adatbázis rétegének az Oracle Cloud Infrastructure (OCI) szolgáltatásban való futtatásához, valamint az alkalmazáshoz és a Microsoft Azure egyéb szintjeihez is. Az élmény hasonló a teljes megoldási verem egyetlen felhőben való futtatásához. 

Ha szeretné futtatni a middleware-t, beleértve a WebLogic-kiszolgálót, az Azure-infrastruktúrán, de az Oracle-adatbázist a OCI-en belül futtatja, tekintse meg a [Weblogic Server Azure-alkalmazások](oracle-weblogic.md)című részt.

Ha az Oracle-megoldások teljes egészében Azure-infrastruktúrán való üzembe helyezését érdekli, tekintse meg az [Oracle virtuálisgép-rendszerképek és azok üzembe helyezése Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>A forgatókönyv áttekintése

A Felhőbeli kapcsolat lehetővé teszi, hogy az Oracle piacvezető alkalmazásait és saját egyéni alkalmazásait az Azure Virtual Machines szolgáltatásban futtassa, miközben élvezi az üzemeltetett adatbázis-szolgáltatások előnyeit a OCI-ben. 

A május 2020-től kezdve a következő alkalmazások egy Felhőbeli konfigurációban vannak hitelesítve:

* E-Business csomag
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle kereskedelmi alkalmazások
* Oracle Hyperion pénzügyi felügyelet

A következő ábra a csatlakoztatott megoldás magas szintű áttekintését mutatja be. Az egyszerűség kedvéért a diagram csak az alkalmazási szintet és az adatszinteket jeleníti meg. Az alkalmazás-architektúrától függően a megoldás további csomagokat is tartalmazhat, például egy WebLogic-fürtöt vagy webes réteget az Azure-ban. További információt a következő részekben talál.

![Az Azure OCI-megoldás áttekintése](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Régió elérhetősége 

A Felhőbeli kapcsolat a következő régiókra korlátozódik:
* Azure USA keleti régiója (EastUS) & OCI Ashburn, VA (USA keleti régiója)
* Azure Egyesült Királyság déli régiója (UKSouth) & OCI London (Egyesült Királyság déli régiója)
* Azure Canada Central (CanadaCentral) & OCI Toronto (Délkelet-Kanada)
* Azure West Europe (WestEurope) & OCI Amsterdam (Hollandia északnyugati régiója)
* Azure Kelet-Japán (JapanEast) & OCI Tokyo (Kelet-Japán)
* Azure West US (WestUS) & OCI San Jose (USA nyugati régiója)

## <a name="networking"></a>Hálózatkezelés

A nagyvállalati ügyfelek gyakran úgy döntenek, hogy különböző üzleti és üzemeltetési okokból több felhőben is diverzifikálják és üzembe helyezik a számítási feladatokat. Az ügyfelek változatossá teszik a felhőalapú hálózatokat az interneten, az IPSec VPN-en vagy a felhőalapú szolgáltató közvetlen csatlakozási megoldásának használatával a helyszíni hálózaton keresztül. A felhőalapú hálózatok összekapcsolásával jelentős mértékű befektetésekre lehet szükség az idő, a pénz, a tervezés, a beszerzés, a telepítés, a tesztelés és a műveletek terén. 

Az Oracle és a Microsoft integrált többfelhős felhasználói élményt nyújt az ügyfelekkel kapcsolatos problémák megoldásához. A Felhőbeli hálózatkezelést úgy kell megalkotni, hogy összekapcsolja a [ExpressRoute](../../../expressroute/expressroute-introduction.md) áramkört Microsoft Azure egy [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) áramkörrel a OCI-ben. Ez a kapcsolat akkor lehetséges, ha egy Azure-ExpressRoute-társítási hely közel van a OCI-FastConnect azonos egyenrangú helyéhez vagy ahhoz. Ez a beállítás lehetővé teszi a két felhő közötti biztonságos és gyors kapcsolódást, anélkül, hogy egy köztes szolgáltatóra lenne szükség.

A ExpressRoute és a FastConnect használatával az ügyfelek az Azure-beli virtuális hálózatokat a OCI-ben virtuális felhőalapú hálózattal is felhasználhatják, ha a magánhálózati IP-címtartomány nem fedi át a területet. A két hálózat társításával lehetővé válik, hogy a virtuális hálózat egy erőforrása kommunikáljon a OCI virtuális felhőalapú hálózatában lévő erőforrásokkal, mintha mindkettő ugyanabban a hálózatban legyen.

## <a name="network-security"></a>Hálózati biztonság

A hálózati biztonság a vállalati alkalmazások egyik kulcsfontosságú összetevője, amely a több felhőalapú megoldás központi eleme. A ExpressRoute és a FastConnect feletti forgalom egy magánhálózaton halad át. Ez a konfiguráció lehetővé teszi az Azure-beli virtuális hálózatok és az Oracle-alapú virtuális felhőalapú hálózatok közötti biztonságos kommunikációt. Nem kell megadnia nyilvános IP-címet az Azure-beli virtuális gépekhez. Ehhez hasonlóan nincs szüksége Internet-átjáróra a OCI-ben. Minden kommunikáció a gépek magánhálózati IP-címén keresztül történik.

Emellett [biztonsági listát](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) is BEÁLLÍTHAT a OCI virtuális felhőalapú hálózati és biztonsági szabályaihoz (az Azure [hálózati biztonsági csoportjaihoz](../../../virtual-network/network-security-groups-overview.md)csatolva). Ezekkel a szabályokkal szabályozhatja a virtuális hálózatokban lévő gépek közötti forgalmat. A hálózati biztonsági szabályok a gép szintjén, az alhálózat szintjén és a virtuális hálózat szintjén is hozzáadhatók.

A [Weblogic Server Azure-alkalmazások](oracle-weblogic.md) mindegyike létrehoz egy hálózati biztonsági csoportot előre konfigurálva, hogy együttműködjön a Weblogic-kiszolgáló portjának konfigurációjáról.
 
## <a name="identity"></a>Identitás

Az identitás a Microsoft és az Oracle közötti partnerség egyik legfontosabb pillére. Jelentős munka történt az [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) [Azure Active Directory](../../../active-directory/index.yml) (Azure ad) szolgáltatással való integrálásához. Az Azure AD a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása. A felhasználók bejelentkezhetnek, és hozzáférhetnek a különböző erőforrásokhoz az Azure AD súgójának használatával. Az Azure AD lehetővé teszi a felhasználók és az engedélyeik kezelését is.

Jelenleg ez az integráció lehetővé teszi, hogy egyetlen központi helyen kezelje a Azure Active Directory. Az Azure AD szinkronizálja a címtárban bekövetkezett változásokat a megfelelő Oracle-címtárral, és az egyszeri bejelentkezést használja a felhőalapú Oracle-megoldásokhoz.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg az Azure és a OCI közötti [Felhőbeli hálózattal](configure-azure-oci-networking.md) . 

A OCI kapcsolatos további információkért és tanulmányokhoz tekintse meg az [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) dokumentációját.
