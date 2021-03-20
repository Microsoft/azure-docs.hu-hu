---
title: Fürtök létrehozása Windows Serveren és Linuxon
description: Service Fabric-fürtök Windows Serveren és Linuxon futnak. A Windows Servert vagy Linux rendszert futtató bárhonnan telepítheti és futtathatja Service Fabric alkalmazásokat.
services: service-fabric
documentationcenter: .net
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: bbfdc0a30aa673e8602ec9233fde4236c99ef5aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97882211"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Az Azure-beli Service Fabric-fürtök áttekintése
A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. A fürt részét képező számítógépet vagy virtuális gépet fürtcsomópont-csomópontnak nevezzük. A fürtök több ezer csomópontra is méretezhetők. Ha új csomópontokat ad hozzá a fürthöz, Service Fabric a csomópontok számának megnövekedésével kiegyensúlyozza a szolgáltatás partíciójának replikáit és példányait. Az alkalmazások teljes teljesítményének növelése és a memória-hozzáférés csökkentése. Ha a fürt csomópontjait nem használják hatékonyan, csökkentheti a fürt csomópontjainak számát. Service Fabric újra kiegyenlíti a partíciók replikáit és példányait a csomópontok számának csökkenésével, hogy jobban használhassa a hardvert az egyes csomópontokon.

A csomópont típusa a fürtben lévő csomópontok (virtuális gépek) méretét, számát és tulajdonságait határozza meg. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. A csomóponttípusok a fürtcsomópontcsoportok szerepkörének (például „előtér” vagy „háttér”) meghatározására szolgálnak. A fürt több csomóponttípussal is rendelkezhet, de éles fürtök esetében az elsődleges csomóponttípusnak legalább öt (vagy tesztfürtök esetében legalább három) virtuális géppel kell rendelkeznie. [A Service Fabric-rendszerszolgáltatások](service-fabric-technical-overview.md#system-services) elhelyezése az elsődleges csomóponttípusra történik. 

## <a name="cluster-components-and-resources"></a>A fürt összetevői és erőforrásai
Az Azure-beli Service Fabric-fürtök olyan Azure-erőforrások, amelyek más Azure-erőforrásokkal való interakciót és interakciót használnak:
* Virtuális gépek és virtuális hálózati kártyák
* virtuálisgép-méretezési csoportok
* virtuális hálózatokkal
* terheléselosztók
* Storage-fiókok
* nyilvános IP-címek

![Service Fabric fürt][Image]

### <a name="virtual-machine"></a>Virtuális gép
A fürt részét képező [virtuális gépeket](../virtual-machines/index.yml) a rendszer egy csomópontnak nevezi, azonban a fürtcsomópont egy Service Fabric futtatókörnyezeti folyamat. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy sztring). A csomópontok jellemzői, például [elhelyezési tulajdonságok](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Mindegyik gépen vagy virtuális gépen van egy automatikus indítási szolgáltatás, *FabricHost.exe*, amely indításkor elindul, majd elindítja a csomópontot alkotó két végrehajtható fájl, *Fabric.exe* és *FabricGateway.exe*. Az éles üzembe helyezés fizikai vagy virtuális gépenként egy csomópont. Tesztelési forgatókönyvek esetén több csomópontot is tárolhat egyetlen számítógépen vagy virtuális gépen a *Fabric.exe* és *FabricGateway.exe* több példányának futtatásával.

Minden virtuális gép egy virtuális hálózati adapterrel (NIC) van társítva, és minden hálózati adapterhez magánhálózati IP-cím van hozzárendelve.  A virtuális gépeket a hálózati ADAPTERen keresztül egy virtuális hálózathoz és egy helyi Balancerhez rendeli a rendszer.

A fürtben lévő összes virtuális gép egy virtuális hálózatba kerül.  Az azonos csomópont típusú/méretezési csoport összes csomópontja ugyanarra az alhálózatra kerül a virtuális hálózaton.  Ezek a csomópontok csak magánhálózati IP-címekkel rendelkeznek, és nem közvetlenül a virtuális hálózaton kívül vannak címezve.  Az ügyfelek az Azure Load balanceren keresztül érhetik el a csomópontok szolgáltatásait.

### <a name="scale-setnode-type"></a>Méretezési csoport/csomópont típusa
Fürt létrehozásakor meg kell adnia egy vagy több csomópont-típust.  A csomópontok típusában lévő csomópontok vagy virtuális gépek azonos méretűek és jellemzőkkel rendelkeznek, mint például a processzorok száma, a memória, a lemezek száma és a lemez I/O.  Előfordulhat például, hogy az egyik csomópont típusa olyan kis, előtér-virtuális gépek, amelyeken a portok nyitva vannak az interneten, miközben egy másik csomópont-típus lehet az olyan nagyméretű, háttérrendszer-alapú virtuális gépek, amelyek adatfeldolgozást végeznek. Az Azure-fürtökben minden egyes csomópont-típus egy [virtuálisgép-méretezési csoportra](../virtual-machine-scale-sets/index.yml)van leképezve.

A méretezési csoportok segítségével virtuális gépek gyűjteményét telepítheti és kezelheti készletként. Az Azure Service Fabric-fürtben definiált egyes csomópont-típusok külön méretezési csoportot állítanak be. Az Service Fabric Runtime az Azure virtuálisgép-bővítmények használatával bootstrapped a méretezési csoportba tartozó egyes virtuális gépekre. Az egyes csomópont-típusok egymástól függetlenül méretezhetők, az egyes fürtcsomópontokon futó operációs rendszerbeli SKU-t módosíthatja, különböző portokat nyithat meg, és különböző kapacitási metrikákat használhat. A méretezési csoportoknak öt [frissítési tartománya](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) és öt tartalék [tartománya](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) van, és akár 100 virtuális gép is lehet.  Több mint 100 csomópontból álló fürtöket hoz létre több méretezési csoport/csomópont típus létrehozásával.

> [!IMPORTANT]
> A fürthöz tartozó csomópontok számának és a csomópontok típusának (méret, elsődleges, internetkapcsolat, virtuális gépek száma stb.) a kiválasztása fontos feladat.  További információért olvassa el a [fürt kapacitásának megtervezésével kapcsolatos szempontokat](service-fabric-cluster-capacity.md).

További információért olvassa el [Service Fabric csomópont-és virtuálisgép-méretezési](service-fabric-cluster-nodetypes.md)csoportok című témakört.

### <a name="azure-load-balancer"></a>Azure Load Balancer
A virtuálisgép-példányok egy olyan [Azure Load Balancer](../load-balancer/load-balancer-overview.md)mögött vannak, amely egy [nyilvános IP-címmel](../virtual-network/public-ip-addresses.md) és egy DNS-címkével van társítva.  Amikor kiépít egy fürtöt a *&lt; &gt; clustername*, a DNS-nevet, a *&lt; clustername &gt; . &lt; a Location &gt; . cloudapp.Azure.com* a terheléselosztó a méretezési csoport elején lévő DNS-címkéje.

A fürtben lévő virtuális gépek csak [magánhálózati IP-címmel](../virtual-network/private-ip-addresses.md)rendelkeznek.  A felügyeleti forgalom és a szolgáltatás forgalmának továbbítása a nyilvánosan elérhető terheléselosztó használatával történik.  A hálózati forgalom a NAT-szabályok (adott csomópontokhoz/példányokhoz csatlakozó ügyfelek) vagy terheléselosztási szabályok (a forgalom a virtuális gépek ciklikus időszeletelése) felé irányítja át ezeket a gépeket.  A terheléselosztó egy DNS-névvel rendelkező nyilvános IP-címmel rendelkezik, amely a (z *&lt; ) clustername formátumú &gt; . &lt; Location &gt; . cloudapp.Azure.com*.  A nyilvános IP-cím az erőforráscsoport egy másik Azure-erőforrása.  Ha egy fürtben több csomópont-típust határoz meg, a rendszer minden egyes csomópont típusú/méretezési csoporthoz létrehoz egy terheléselosztó-t. Vagy beállíthat egyetlen Load balancert több csomópontos típushoz is.  Az elsődleges csomópont típusának DNS-címkéje *&lt; clustername &gt; . &lt; Location &gt; . cloudapp.Azure.com*, más típusú csomópontok esetén a DNS-címke *&lt; clustername &gt; - &lt; NodeType rendelkezik &gt; . &lt; Location &gt; . cloudapp.Azure.com*.

### <a name="storage-accounts"></a>Tárfiókok
Az [Azure Storage-fiók](../storage/common/storage-introduction.md) és a felügyelt lemezek minden egyes fürtcsomópont-típust támogatnak.

## <a name="cluster-security"></a>Fürtbiztonság
A Service Fabric-fürt a saját erőforrása.  Az Ön feladata, hogy biztosítsa a fürtök védelmét, hogy megakadályozza a jogosulatlan felhasználók csatlakozását. A biztonságos fürt különösen fontos, ha éles számítási feladatokat futtat a fürtön. 

### <a name="node-to-node-security"></a>Csomópontok közötti biztonság
A csomópontok közötti biztonság biztosítja a fürtben lévő virtuális gépek vagy számítógépek közötti kommunikációt. Ez a biztonsági forgatókönyv biztosítja, hogy csak a fürthöz való csatlakozásra jogosult számítógépek vehessenek részt a fürtben lévő alkalmazások és szolgáltatások üzemeltetésében. A Service Fabric X. 509 tanúsítványokat használ a fürt biztonságossá tételéhez és az alkalmazás biztonsági funkcióinak biztosításához.  A fürt forgalmának biztonságossá tételéhez, valamint a fürt és a kiszolgáló hitelesítésének biztosításához a fürt tanúsítványa szükséges.  Önaláírt tanúsítványok is használhatók tesztelési fürtökhöz, de megbízható hitelesítésszolgáltatótól származó tanúsítványt kell használni az üzemi fürtök biztonságossá tételéhez.

További információkért olvassa el a [csomópontok közötti biztonsági](service-fabric-cluster-security.md#node-to-node-security) tudnivalókat.

### <a name="client-to-node-security"></a>Az ügyfél és a csomópont közötti biztonság
Az ügyfél és a csomópont közötti biztonság hitelesíti az ügyfeleket, és segít a fürtben lévő ügyfelek és egyes csomópontok közötti kommunikáció biztonságossá tételében. Ez a típusú biztonság biztosítja, hogy csak a jogosult felhasználók férhessenek hozzá a fürthöz és a fürtön üzembe helyezett alkalmazásokhoz. Az ügyfeleket egyedileg azonosítják az X. 509 tanúsítvány biztonsági hitelesítő adataival. Tetszőleges számú választható ügyféltanúsítvány használható a rendszergazda vagy a felhasználói ügyfelek hitelesítésére a fürttel.

Az ügyféltanúsítványok mellett Azure Active Directory is konfigurálható úgy, hogy az ügyfeleket a fürttel hitelesítse.

További információért olvassa el az [ügyfél és a csomópont közötti biztonság](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés
Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-erőforrások részletes hozzáférés-vezérlésének hozzárendelését.  Különböző hozzáférési szabályokat rendelhet hozzá az előfizetésekhez, az erőforráscsoportokhöz és az erőforrásokhoz.  Az Azure RBAC-szabályok öröklik az erőforrás-hierarchiát, kivéve, ha a felülbírálása alacsonyabb szinten történik.  Az Azure RBAC-szabályokkal bármilyen felhasználói vagy felhasználói csoportot hozzárendelhet a HRE, így a kijelölt felhasználók és csoportok módosíthatják a fürtöt.  További információért olvassa el az [Azure RBAC áttekintését](../role-based-access-control/overview.md).

A Service Fabric a hozzáférés-vezérlést is támogatja, hogy korlátozza a hozzáférést bizonyos fürt műveleteihez a különböző felhasználói csoportok esetében. Ez segít a fürt biztonságosabbá tételében. A fürthöz csatlakozó ügyfelek esetében két hozzáférés-vezérlési típus támogatott: rendszergazdai szerepkör és felhasználói szerepkör.  

További információért olvassa el [Service Fabric szerepköralapú hozzáférés-vezérlés](service-fabric-cluster-security.md#service-fabric-role-based-access-control)című témakört.

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok) 
Hálózati biztonsági csoportok (NSG) egy alhálózat, virtuális gép vagy adott hálózati adapter bejövő és kimenő forgalmát vezérlik.  Alapértelmezés szerint, ha több virtuális gép kerül ugyanarra a virtuális hálózatra, akkor bármely porton keresztül kommunikálhatnak egymással.  Ha korlátozni szeretné a számítógépek közötti kommunikációt, megadhatja a NSG a hálózat szegmentálásához vagy a virtuális gépek elkülönítéséhez.  Ha egy fürtben több csomópont-típus található, akkor a NSG az alhálózatokra is alkalmazhatja, hogy megakadályozza, hogy a különböző csomópont-típusokhoz tartozó gépek egymással kommunikáljanak egymással.  

További információkért lásd: [biztonsági csoportok](../virtual-network/network-security-groups-overview.md)

## <a name="scaling"></a>Méretezés

Az alkalmazás iránti igények időbeli változása. Előfordulhat, hogy növelnie kell a fürt erőforrásait, hogy megfeleljen az alkalmazások megnövekedett munkaterhelésének vagy a hálózati forgalomnak, vagy csökkentenie kell a fürterőforrások mennyiségét. Service Fabric-fürt létrehozása után vízszintesen méretezheti a fürtöt (a csomópontok számának módosítása) vagy függőlegesen (a csomópontok erőforrásainak módosítása). A fürtöt bármikor méretezheti, még akkor is, ha a munkaterhelések futnak a fürtön. A fürt skálázása esetén az alkalmazások is automatikusan méretezhetők.

További információért olvassa el az [Azure-fürtök méretezését](service-fabric-cluster-scaling.md)ismertető témakört.

## <a name="upgrading"></a>Frissítés
Az Azure Service Fabric-fürt olyan erőforrás, amelyet Ön birtokol, de részben a Microsoft felügyeli. A Microsoft feladata, hogy kijavítja a mögöttes operációs rendszert, és Service Fabric Runtime-frissítéseket végezzen a fürtön. Beállíthatja, hogy a fürt automatikusan megkapja az automatikus futtatókörnyezet-frissítéseket, amikor a Microsoft új verziót szabadít fel, vagy egy támogatott futásidejű verziót szeretne kiválasztani. A futtatókörnyezet frissítésein kívül a fürtkonfiguráció, például a tanúsítványok vagy az alkalmazások portjai is frissíthetők.

További információért olvassa el a [fürtök frissítését](service-fabric-cluster-upgrade.md)ismertető témakört.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A következő operációs rendszereket futtató virtuális gépeken hozhat létre fürtöket:

| Operációs rendszer | A legkorábbi támogatott Service Fabric verziója | Az utolsó támogatott Service Fabric verziója |
| --- | --- | --- | 
| Windows Server 2019 | 6.4.654.9590 | N/A |
| Windows Server 2016 | Az összes verzió | N/A |
| Windows Server 20H2 | 7.2.445.9590 | N/A |
| Windows Server 1809 | 6.4.654.9590 | 7.2.445.9590 |
| Windows Server 1803 | 6.4 | 7.2.445.9590 |
| Windows Server 1709 | 6.0 | 7.2.445.9590 |
| Windows Server 2012 | Az összes verzió | N/A | 
| Linux Ubuntu 16,04 | 6.0 | N/A |
| Linux Ubuntu 18,04 | 7.1 | N/A |

További információ: [támogatott fürtözött verziók az Azure-ban](./service-fabric-versions.md#supported-operating-systems)

> [!NOTE]
> Ha úgy dönt, hogy Service Fabrict telepít a Windows Server 1709-es verziójára, vegye figyelembe, hogy (1) nem hosszú távú karbantartási ág, ezért előfordulhat, hogy a későbbi verziókat kell áthelyeznie, és (2) Ha tárolókat helyez üzembe, a Windows Server 2016-re épülő tárolók nem működnek a Windows Server 1709 rendszeren
>


## <a name="next-steps"></a>Következő lépések
További információ az Azure-fürtök [biztonságossá](service-fabric-cluster-security.md)tételéről, [méretezéséről](service-fabric-cluster-scaling.md)és [frissítéséről](service-fabric-cluster-upgrade.md) .

További információ a [Service Fabric támogatási lehetőségeiről](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG