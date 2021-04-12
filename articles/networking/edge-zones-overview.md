---
title: Az Azure Edge Zone előzetes verziójának bemutatása
description: Ismerje meg a Microsoft Azure Edge Zone szolgáltatásának Edge Computing-ajánlatait.
services: vnf-manager
author: cherylmc
ms.service: vnf-manager
ms.topic: article
ms.date: 01/13/2021
ms.author: cherylmc
ms.openlocfilehash: 04555303d5128db6c183d27a0c5fcb69063fdc28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185410"
---
# <a name="about-azure-edge-zone-preview"></a>Az Azure Edge Zone előzetes verziójának bemutatása

Az Azure Edge Zone olyan Microsoft Azure kínál, amely az adatfeldolgozást a felhasználóhoz közelebb teszi. A virtuális gépeket, tárolókat és más kiválasztott Azure-szolgáltatásokat üzembe helyezheti peremhálózati zónákba az alkalmazások alacsony késésű és nagy teljesítményű követelményeinek kielégítése érdekében.

Az Edge-zónák tipikus használati esetei a következők:

- Valós idejű parancs és vezérlés a roboticsban.
- Valós idejű elemzések és következtetések a mesterséges intelligencia és a gépi tanulás segítségével.
- Gépi jövőkép.
- Távoli renderelés vegyes valósághoz és VDI-forgatókönyvekhez.
- Többrésztvevős játékok.
- Médiaadatfolyam-továbbítás és tartalmak továbbítása.
- Felügyelet és biztonság.

Az Azure Edge-zónák három típusa létezik:

- Azure Edge-zónák
- Azure Edge-zónák szolgáltatóval
- Azure Private Edge-zónák

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge-zónák

![Azure Edge-zónák](./media/edge-zones-overview/edge-zones.png "Azure Edge-zónák")

Az Azure Edge Zones olyan kis helyigényű Azure-bővítmények, amelyek az Azure-régióktól távol lévő, populációs központokban vannak elhelyezve. Az Azure Edge Zones támogatja a virtuális gépeket, tárolókat és az Azure-szolgáltatások kiválasztott készletét, amely lehetővé teszi a késésre érzékeny és az adatátviteli igényű alkalmazások bezárását a végfelhasználók számára. Az Azure Edge-zónák a Microsoft globális hálózatának részét képezik. Biztonságos, megbízható és nagy sávszélességű kapcsolatot biztosítanak a peremhálózati zónában futó alkalmazások között a felhasználóhoz közel. Az Azure Edge-zónák tulajdonosai és üzemeltetői a Microsoft. Ugyanezeket az Azure-eszközöket és-portált használhatja a szolgáltatások peremhálózati zónákba való felügyeletéhez és üzembe helyezéséhez.

A tipikus használati esetek a következők:

- Játékok és játékok folyamatos átvitele.
- Médiaadatfolyam-továbbítás és tartalmak továbbítása.
- Valós idejű elemzések és következtetések a mesterséges intelligencia és a gépi tanulás segítségével.
- Megjelenítés a vegyes valósághoz.

Az Azure Edge-zónák a következő metró területeken lesznek elérhetők:

- New York, NY
- Los Angeles, CA
- Miami, FL

További információért [vegye fel a kapcsolatot az Edge Zone csapatával](https://aka.ms/EdgeZones) .

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Azure Edge-zónák szolgáltatóval

![Peremhálózati zónák a fuvarozóval](./media/edge-zones-overview/edge-carrier.png "Peremhálózati zónák a fuvarozóval")

Az Azure Edge Zones és a Carrier olyan kis helyigényű Azure-bővítmények, amelyek a lakossági adatközpontokban vannak elhelyezve a mobil operátorok adatközpontjában. A szolgáltatói infrastruktúrával rendelkező Azure Edge Zone egy hop-re van helyezve a mobilszolgáltató 5G hálózatán. Ez az elhelyezés 10 ezredmásodpercnél rövidebb késést biztosít a mobileszközök alkalmazásai számára.

A fuvarozóval rendelkező Azure Edge-zónák üzembe helyezése a mobileszközök adatközpontjában történik, és a Microsoft globális hálózatához csatlakozik. Biztonságos, megbízható és nagy sávszélességű kapcsolatot biztosítanak a felhasználóhoz közel lévő alkalmazások között. A fejlesztők ugyanazzal a jól ismert eszközökkel építhetnek és helyezhetnek üzembe szolgáltatásokat a peremhálózati zónákban.

A tipikus használati esetek a következők:

- Játékok és játékok folyamatos átvitele.
- Médiaadatfolyam-továbbítás és tartalmak továbbítása.
- Valós idejű elemzések és következtetések a mesterséges intelligencia és a gépi tanulás segítségével.
- Megjelenítés a vegyes valósághoz.
- Csatlakoztatott autók.
- Tele-Medicine.

A peremhálózati zónák a következő operátorokkal együttműködve lesznek elérhetők:

- &T (Atlanta, Dallas és Los Angeles)

Az 5G-hálózatokhoz csatlakozó, optimalizált és méretezhető alkalmazásokkal rendelkező ISV-ket mostantól az Azure Edge Zones új, a&T-re vonatkozó előzetes verzióját használhatja az alacsony késésű platformok, a mobil és a csatlakoztatott forgatókönyvek kiépítéséhez és kipróbálásához. Regisztráljon a korai örökbefogadó programra, és használja ki a biztonságos, nagy sávszélességű kapcsolat előnyeit.

További információért [vegye fel a kapcsolatot az Edge Zone csapatával](https://aka.ms/EdgeZones) .

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Azure Private Edge-zónák

![Saját peremhálózati zónák](./media/edge-zones-overview/private-edge.png "Saját peremhálózati zónák")

Az Azure Private Edge Zones az Azure kis helyigényű bővítményei, amelyek a helyszínen vannak elhelyezve. Az Azure Private Edge Zone az [Azure stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) platformon alapul. Kis késésű hozzáférést biztosít a helyszínen üzembe helyezett számítástechnikai és tárolási szolgáltatásokhoz. A privát peremhálózati zóna lehetővé teszi, hogy a szoftvergyártók és a virtualizált hálózati függvények (VNFs-EK) alkalmazásait az [Azure által felügyelt alkalmazásokként](https://azure.microsoft.com/services/managed-applications/) , valamint a helyszíni virtuális gépekkel és tárolókkal is telepítse. Ezek a VNFs tartalmazhatnak mobil csomagok magjait, útválasztókat, tűzfalakat és SD-WAN készülékeket. Az Azure Private Edge Zone egy Felhőbeli natív kialakítási megoldás, amely lehetővé teszi a VNFs és az alkalmazások életciklusának kezelését a Azure Portal.

Az Azure Private Edge Zone lehetővé teszi, hogy a helyszínen fejlesszen és helyezzen üzembe alkalmazásokat az Azure-ban az alkalmazások létrehozásához és üzembe helyezéséhez használt, ismerős eszközök használatával.

Emellett a következőket is lehetővé teszi:

- Privát mobil hálózatok futtatása (privát HTH, privát 5G).
- A biztonsági funkciók, például a tűzfalak implementálása.
- A helyszíni hálózatokat több ág és az Azure között is kiterjesztheti, ha SD-WAN-berendezéseket használ ugyanazon a saját peremhálózati zónában lévő készülékeken, és felügyelheti őket az Azure-ból.

A tipikus használati esetek a következők:

- Valós idejű parancs és vezérlés a roboticsban.
- Valós idejű elemzések és a mesterséges intelligenciával és gépi tanulással kapcsolatos információk.
- Gépi jövőkép.
- Távoli renderelés vegyes valósághoz és VDI-forgatókönyvekhez.
- Felügyelet és biztonság.

A VNF-gyártók, az ISV-és az MSP-partnerek gazdag ökoszisztémája lehetővé teszi, hogy a saját peremhálózati zónákat használó teljes körű megoldásokat engedélyezzék. További információért [forduljon a Private Edge Zone csapatához](https://aka.ms/EdgeZonesPartner) .

### <a name="private-edge-zone-partners"></a><a name="private-edge-partners"></a>Private Edge Zone-partnerek

![Private Edge Zone-partnerek](./media/edge-zones-overview/partners.png "Private Edge Zones-partnerek")

#### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>Virtualizált hálózati függvények (VNFs)

##### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>Virtualizált kialakult Packet Core (vEPC) mobil hálózatokhoz

- [Megerősített hálózatok](https://www.affirmednetworks.com/)
- [Celona](https://www.celona.io/azure-edge)
- [Druida szoftver](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Mavenir](https://mavenir.com/)
- [Metaswitch](https://www.metaswitch.com/)
- [Nokia digitális automatizálási felhő](https://www.dac.nokia.com/)

##### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>Mobil rádiós partnerek

- [Celona](https://www.celona.io/azure-edge)
- [Commscope ricsaj](https://support.ruckuswireless.com/)

##### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>SD-WAN-szállítók

- [128 Technology](https://www.128technology.com/)
- [NetFoundry](https://netfoundry.io/)
- [Nuage hálózatok Nokia-ból](https://www.nuagenetworks.net/)
- [Fordítva hálózatok](https://www.versa-networks.com/)
- [VMware SD – WAN by Velocloud](https://www.velocloud.com/)

##### <a name="router-vendors"></a><a name="router-vendors"></a>Útválasztó-szállítók

- [Arista](https://www.arista.com/)

##### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>Tűzfal-szállítók

- [Palo Alto Networks](https://www.paloaltonetworks.com/)

##### <a name="managed-solutions-providers-mobile-operators-and-global-system-integrators-gsis"></a><a name="msp-mobile"></a>Felügyelt megoldások szolgáltatói: mobilszolgáltatók és globális rendszerintegrátorok (GSIs-k)

| GSIs és operátorok | Mobilszolgáltatók |
| --- | --- |
| Amdocs                       | Etisalat             |
| Amerikai torony               | NTT Communications   |
| CenturyLink                  | Proximus             |
| Expeto                       | Rogers               |
| Összevont vezeték nélküli           | SK Telecom           |
| Infosys                      | Telefonica           |
| Technikai Mahindra                | Telstra              |
|                              | Vodafone             |

[Lépjen kapcsolatba a Private Edge Zone csapatával](https://aka.ms/EdgeZonesPartner) , és tájékozódjon arról, hogyan válhat partnernek.

### <a name="private-edge-zone-solutions"></a><a name="solutions-private-edge"></a>Saját peremhálózati zónák megoldásai

#### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Privát mobileszköz a privát peremhálózati zónákban

![Privát mobileszköz a privát peremhálózati zónákban](./media/edge-zones-overview/mobile-networks.png "Privát mobileszköz a privát peremhálózati zónákban")

Most már üzembe helyezhet egy privát mobil hálózatot a saját peremhálózati zónákban. A privát mobileszközök rendkívül alacsony késést és nagy kapacitást, valamint az üzleti szempontból kritikus fontosságú alkalmazások számára szükséges megbízható és biztonságos vezeték nélküli hálózatot teszik lehetővé.

A privát mobil hálózatok a következő esetekben engedélyezhetik a hasonló forgatókönyveket:
- Az automatizált vezetett járművek (AGVs-EK) irányítása és vezérlése a raktárakban.
- Valós idejű kommunikáció a robotok között az intelligens gyárakban.
- Kibővített valóság és virtuális valóság Edge-alkalmazások.

A virtualizált kialakult Packet Core (vEPC) hálózati függvény egy privát mobil hálózat agya. Most már üzembe helyezhet egy vEPC a saját peremhálózati zónákban. A privát peremhálózati zónákban elérhető vEPC-partnerek listáját itt tekintheti meg: [VEPC ISV](#vEPC)-ket.

A privát mobil hálózati megoldások saját peremhálózati zónákban való üzembe helyezéséhez más összetevőkre, például mobil hozzáférési pontokra, SIM-kártyákra és más VNFs (például útválasztók) van szükség. A saját mobil hálózat beállítása kritikus fontosságú a licenccel rendelkező vagy nem licencelt spektrumhoz való hozzáféréshez. Előfordulhat, hogy segítségre van szüksége a RÁDIÓFREKVENCIÁs tervezéssel, a fizikai elrendezéssel, a telepítéssel és a támogatással kapcsolatban. A partnerek listáját a következő témakörben tekintheti meg: [mobil rádiós partnerek](#mobile-radio).

A Microsoft olyan partneri ökoszisztémát biztosít, amely segítséget nyújt a folyamat valamennyi aspektusához. A partnerek segítséget nyújthatnak a hálózat megtervezésében, a szükséges eszközök megvásárlásában, a hardver beállításában és az Azure-beli konfiguráció kezelésében. A Microsofttal szorosan integrált, ellenőrzött partnerek készlete biztosítja, hogy a megoldás megbízható és könnyen használható legyen. Az alapvető forgatókönyvekre koncentrálhat, és a Microsoft és partnerei számára is hasznos lehet a REST használatának segítése.

#### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>SD-WAN a saját peremhálózati zónái

![SD-WAN a saját peremhálózati zónái](./media/edge-zones-overview/sd-wan.png "SD-WAN a saját peremhálózati zónái")

Az SD-WAN lehetővé teszi, hogy olyan nagyvállalati szintű hálózatokat (WAN) hozzon létre, amelyek az alábbi előnyökkel rendelkeznek:

- Nagyobb sávszélesség
- Nagy teljesítményű hozzáférés a felhőhöz
- Szolgáltatás beszúrása
- Megbízhatóság
- Szabályzatkezelés
- Kiterjedt hálózat láthatósága

Az SD-WAN zökkenőmentes fiókirodai kapcsolatot biztosít, amely a redundáns központi vezérlőkből áll, és alacsonyabb a tulajdonlási díja.
Az SD-WAN privát peremhálózati zónái lehetővé teszik, hogy a CAPEX-központú modellből egy szolgáltatott szoftveres (SaaS) modellre váltson, hogy csökkentse az informatikai költségvetéseket. Az Ön által választott SD-WAN-partnereket, Orchestrator vagy vezérlőket használhat az új szolgáltatások engedélyezéséhez, és a teljes hálózaton keresztül propagálhatja azokat.

## <a name="next-steps"></a>Következő lépések

További információért forduljon a következő csapatokhoz:

* [Peremhálózati zóna csapata](https://aka.ms/EdgeZones)
* [Private Edge Zone-csapat, hogy partnerré váljon](https://aka.ms/EdgeZonesPartner)
