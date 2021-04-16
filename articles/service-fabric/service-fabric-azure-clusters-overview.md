---
title: Fürtök létrehozása Windows Serveren és Linuxon
description: Service Fabric fürtök Windows Serveren és Linuxon futnak. A windowsos és linuxos Service Fabric bárhol telepíthet és futtathat alkalmazásokat.
services: service-fabric
documentationcenter: .net
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 4aed4ab38db9f8d8b95647b6662245c93778afed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520156"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Az Azure Service Fabric fürtök áttekintése
A Service Fabric virtuális vagy fizikai gépek hálózatra csatlakoztatott készlete, amelyben mikroszolgáltatások vannak üzembe állítva és kezelve. A fürt részét képezi gépeket vagy virtuális gépeket fürtcsomópontnak nevezzük. A fürtök több ezer csomópontra méretezhetők. Ha új csomópontokat ad hozzá a fürthöz, a Service Fabric újra egyensúlyba hoz a szolgáltatáspartíció replikáit és példányait a csomópontok megnövekedett száma között. Az alkalmazás általános teljesítménye javul, és csökken a memória-hozzáférésért való hozzáférésért való elégedettség. Ha a fürt csomópontjai nincsenek hatékonyan használva, csökkentheti a fürtben lévő csomópontok számát. Service Fabric újra kiegyensúlyozja a partícióreplikák és -példányok számát a csökkentett számú csomóponton, hogy jobban kihasználhatja az egyes csomópontok hardverét.

A csomóponttípus határozza meg a fürtben lévő csomópontok (virtuális gépek) méretét, számát és tulajdonságait. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. A csomóponttípusok a fürtcsomópontcsoportok szerepkörének (például „előtér” vagy „háttér”) meghatározására szolgálnak. A fürt több csomóponttípussal is rendelkezhet, de éles fürtök esetében az elsődleges csomóponttípusnak legalább öt (vagy tesztfürtök esetében legalább három) virtuális géppel kell rendelkeznie. [A Service Fabric-rendszerszolgáltatások](service-fabric-technical-overview.md#system-services) elhelyezése az elsődleges csomóponttípusra történik. 

## <a name="cluster-components-and-resources"></a>Fürtösszetevők és -erőforrások
Az Service Fabric Azure-beli fürt egy olyan Azure-erőforrás, amely más Azure-erőforrásokat használ és kommunikál:
* Virtuális gépek és virtuális hálózati kártyák
* virtuálisgép-méretezési csoportok
* virtuális hálózatokkal
* terheléselosztók
* tárfiókok
* nyilvános IP-címek

![Service Fabric fürt][Image]

### <a name="virtual-machine"></a>Virtuális gép
A [fürt részét](../virtual-machines/index.yml) képezi virtuális gép neve azonban csomópont, technikailag azonban a fürtcsomópont Service Fabric futásidejű folyamat. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy sztring). A csomópontok olyan jellemzőkkel rendelkeznek, mint [az elhelyezési tulajdonságok.](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) Minden gép vagy virtuális gép rendelkezik egy automatikus indítási szolgáltatással *(FabricHost.exe,* amely rendszerindításkor indul el, majd elindít két végrehajtható fájlt ( *Fabric.exe* és *FabricGateway.exe*). Az éles környezet fizikai vagy virtuális gépenként egy csomópont. Tesztelési forgatókönyvek esetén több csomópontot is futtathat egyetlen gépen vagy virtuális gépen, ha több példányt futtat aFabric.exe *és* *FabricGateway.exe.*

Minden virtuális gép egy virtuális hálózati adapterhez (NIC) van társítva, és minden hálózati adapterhez magánhálózati IP-cím van hozzárendelve.  A virtuális gép a hálózati adapteren keresztül van hozzárendelve egy virtuális hálózathoz és egy helyi kiegyensúlyozott hálózathoz.

A fürt összes virtuális gépe egy virtuális hálózatban van elhelyezve.  Az azonos csomóponttípus/méretezési csoport összes csomópontja ugyanazon az alhálózaton található a virtuális hálózaton.  Ezek a csomópontok csak magánhálózati IP-címmel és közvetlenül nem címezhetők a virtuális hálózaton kívül.  Az ügyfelek az Azure Load Balanceren keresztül férhetnek hozzá a csomópontokon található szolgáltatásokhoz.

### <a name="scale-setnode-type"></a>Méretezésicsoport/csomópont típusa
Fürt létrehozásakor egy vagy több csomóponttípust kell meghatároznia.  A csomópontok vagy virtuális gépek azonos méretűek és ugyanolyan jellemzőkkel rendelkeznek, mint a processzorok száma, a memória, a lemezek száma és a lemez I/O-i.  Az egyik csomóponttípus lehet például kis méretű, internetre nyitott portokkal, míg egy másik csomóponttípus az adatokat feldolgozó, nagy méretű, háttér virtuális gépekhez. Az Azure-fürtökben minden csomóponttípus egy virtuálisgép-méretezési [csoportra van leképezve.](../virtual-machine-scale-sets/index.yml)

Méretezésicsoportokkal virtuálisgép-gyűjteményeket helyezhet üzembe és kezelhet készletként. Az Azure-beli fürtökben meghatározott Service Fabric külön méretezési csoportot állít be. A Service Fabric futtatás a méretezési készlet minden virtuális gépére ki lesz indítva Azure-beli virtuálisgép-bővítmények használatával. Az egyes csomóponttípusokat egymástól függetlenül skálázhatja felfelé vagy lefelé, módosíthatja az egyes fürtcsomópontokon futó operációsrendszer-termékváltozatokat, különböző nyitott portkészletekkel rendelkezik, és különböző kapacitásmetrikákat használhat. A méretezési készlet [öt](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) frissítési és [öt](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) tartalék tartományból áll, és legfeljebb 100 virtuális gépből áll.  Több mint 100 csomópontból több fürtöt hozhat létre több méretezési csoport/csomóponttípus létrehozásával.

> [!IMPORTANT]
> Fontos feladat a fürt csomóponttípusai számának és tulajdonságainak (méret, elsődleges, internetkapcsolat, virtuális gépek száma stb.) kiválasztása.  További információkért olvassa el a fürtkapacitás [tervezésével kapcsolatos szempontokat.](service-fabric-cluster-capacity.md)

További információ: [csomóponttípusok Service Fabric virtuálisgép-méretezési készletek.](service-fabric-cluster-nodetypes.md)

### <a name="azure-load-balancer"></a>Azure Load Balancer
A virtuálisgép-példányok egy Azure Load Balancer mögött [vannak összekapcsolva,](../load-balancer/load-balancer-overview.md)amely egy nyilvános [IP-címhez](../virtual-network/public-ip-addresses.md) és DNS-címkéhez van társítva.  Amikor fürtnévvel létesít fürtöt, *&lt; &gt;* a DNS-név, *&lt; a fürtnév &gt; . &lt; A &gt; location .cloudapp.azure.com* a méretezési csoport előtt a terheléselosztáshoz társított DNS-címke.

A fürtben lévő virtuális gépek csak [magánhálózati IP-címmel vannak.](../virtual-network/private-ip-addresses.md)  A felügyeleti forgalom és a szolgáltatásforgalom a nyilvános terheléseltöltőn keresztül van irányítva.  A hálózati forgalom ezekre a gépekre NAT-szabályokon (az ügyfelek adott csomópontokhoz/példányokhoz csatlakoznak) vagy terheléselosztási szabályokon keresztül (a forgalom ciklikus időkorreklikusan halad a virtuális gépekhez).  A terheléselosztáshoz társított nyilvános IP-cím a következő formátumú DNS-névvel *&lt; rendelkezik: &gt; fürtnév. &lt; location &gt; .cloudapp.azure.com*.  A nyilvános IP-cím az erőforráscsoport egy másik Azure-erőforrása.  Ha több csomóponttípust határoz meg egy fürtben, a rendszer minden csomóponttípushoz/méretezési csoporthoz létrehoz egy terheléselelosztást. Vagy több csomóponttípushoz is beállíthat egyetlen terheléselosztást.  Az elsődleges csomópont típusa a fürtnév *&lt; DNS-címkével &gt; rendelkezik. &lt; location &gt; .cloudapp.azure.com*, a többi csomóponttípus DNS-címkéje *&lt; fürtnév &gt; - &lt; &gt; csomóponttípus. &lt; location &gt; .cloudapp.azure.com*.

### <a name="storage-accounts"></a>Tárfiókok
Minden fürtcsomóponttípust támogat egy [Azure Storage-fiók és](../storage/common/storage-introduction.md) egy felügyelt lemez.

## <a name="cluster-security"></a>Fürtbiztonság
A Service Fabric a saját erőforrása.  Az Ön felelőssége, hogy biztosítsa a fürtök biztonságát, hogy megakadályozza a jogosulatlan felhasználók csatlakozását. A biztonságos fürt akkor különösen fontos, ha éles számítási feladatokat futtat a fürtön. 

### <a name="node-to-node-security"></a>Csomópontok és csomópontok biztonsága
A csomópontok közötti biztonság biztosítja a fürtben lévő virtuális gépek vagy számítógépek közötti kommunikációt. Ez a biztonsági forgatókönyv biztosítja, hogy csak a fürthöz való csatlakozásra jogosult számítógépek vehessenek részt a fürtben található alkalmazások és szolgáltatások üzemeltetésében. Service Fabric X.509-tanúsítványokat használ a fürtök biztonságossá tere és az alkalmazásbiztonsági funkciók biztosítása érdekében.  A fürt forgalmának biztonságához, valamint a fürt- és kiszolgálóhitelesítéshez fürt tanúsítványra van szükség.  Az önaírt tanúsítványok használhatók tesztfürtökhöz, de az éles fürtök biztonságossá teteméhez egy megbízható hitelesítésszolgáltatótól származó tanúsítványt kell használni.

További információ: [Csomópontok és csomópontok biztonsága](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Ügyfél–csomópont biztonság
Az ügyfél és a csomópont közötti biztonság hitelesíti az ügyfeleket, és biztonságossá teszi az ügyfél és a fürt egyes csomópontjai közötti kommunikációt. Ez a biztonsági típus biztosítja, hogy csak a jogosult felhasználók férnek hozzá a fürthöz és a fürtre telepített alkalmazásokhoz. Az ügyfelek egyedileg azonosíthatók az X.509-tanúsítvány biztonsági hitelesítő adataival. A rendszergazdai vagy felhasználói ügyfelek fürtön való hitelesítéséhez tetszőleges számú választható ügyféltanúsítvány használható.

Az ügyféltanúsítványok mellett a Azure Active Directory is konfigurálható az ügyfelek fürtön való hitelesítésére.

További információ: [Ügyfél–csomópont biztonság](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés
Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi, hogy finomhangolt hozzáférés-vezérlést rendeljen az Azure-erőforrásokhoz.  Különböző hozzáférési szabályokat rendelhet előfizetéshez, erőforráscsoportokhoz és erőforrásokhoz.  Az Azure RBAC-szabályok az erőforrás-hierarchiában öröklődik, hacsak felül nem bírálja őket alacsonyabb szinten.  Az Azure RBAC-szabályokkal bármilyen felhasználói vagy felhasználói csoportot hozzárendelhet az AAD-hez, hogy a kijelölt felhasználók és csoportok módosítsák a fürtöt.  További információkért olvassa el az [Azure RBAC áttekintését.](../role-based-access-control/overview.md)

Service Fabric a hozzáférés-vezérlést is támogatja, hogy bizonyos fürtműveleteket a különböző felhasználói csoportokra korlátozza. Ez segít biztonságossá tenni a fürtöt. A fürthöz csatlakozó ügyfelek két hozzáférés-vezérlési típust támogatnak: rendszergazdai szerepkört és felhasználói szerepkört.  

További információ: Service Fabric [hozzáférés-vezérlése.](service-fabric-cluster-security.md#service-fabric-role-based-access-control)

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok) 
A hálózati biztonsági csoportok (NSG-k) egy alhálózat, virtuális gép vagy adott hálózati adapter bejövő és kimenő forgalmát szabályják.  Alapértelmezés szerint ha több virtuális gép van ugyanazon a virtuális hálózaton, akkor bármilyen porton keresztül kommunikálhatnak egymással.  A gépek közötti kommunikáció korlátozásához definiálhat NSG-ket a hálózat szegmentálhatja vagy elkülönítheti egymástól a virtuális gépeket.  Ha egy fürtben több csomóponttípus található, NSG-ket alkalmazhat az alhálózatok esetében, hogy megakadályozza a különböző csomóponttípusokhoz tartozó gépek egymással való kommunikációját.  

További információ: Biztonsági [csoportok](../virtual-network/network-security-groups-overview.md)

## <a name="scaling"></a>Méretezés

Az alkalmazásigények idővel változnak. Előfordulhat, hogy növelnie kell a fürterőforrásokat, hogy megfeleljen az alkalmazások megnövekedett terhelésének vagy a hálózati forgalomnak, vagy csökkentenie kell a fürterőforrásokat, ha csökken az igény. Miután létrehozta Service Fabric fürtöt, horizontálisan skálázhatja a fürtöt (módosíthatja a csomópontok számát) vagy függőlegesen (módosíthatja a csomópontok erőforrásait). A fürt bármikor skálázhatja, még akkor is, ha a fürtön számítási feladatok futnak. A fürt méretezése során az alkalmazások is automatikusan skálázódnak.

További információ: [Azure-fürtök skálázása.](service-fabric-cluster-scaling.md)

## <a name="upgrading"></a>Frissítés
Az Azure Service Fabric-fürt az Ön tulajdonában áll, de részben a Microsoft kezeli. A Microsoft felelős a mögöttes operációs rendszer javításáért és Service Fabric frissítéséért a fürtön. Beállíthatja, hogy a fürt automatikusan megkapja a futásidejű frissítéseket, amikor a Microsoft új verziót ad ki, vagy kiválaszthatja a kívánt támogatott futásidejű verziót. A futásidejű frissítések mellett a fürtkonfigurációkat, például a tanúsítványokat vagy az alkalmazásportokat is frissítheti.

További információ: [Fürtök frissítése.](service-fabric-cluster-upgrade.md)

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
További [információért tekintse meg az Azure-ban támogatott](./service-fabric-versions.md) verziókat


## <a name="next-steps"></a>Következő lépések
További információ az [Azure-fürtök](service-fabric-cluster-security.md)biztonságossá [tétele,](service-fabric-cluster-scaling.md)skálázása és frissítése. [](service-fabric-cluster-upgrade.md)

További információ [a Service Fabric lehetőségekről.](service-fabric-support.md)

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG