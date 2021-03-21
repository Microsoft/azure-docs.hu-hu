---
title: Infrastruktúra-konfigurációk és-műveletek SAP HANA az Azure-ban | Microsoft Docs
description: Az Azure Virtual Machines szolgáltatásban üzembe helyezett SAP HANA rendszerek üzemeltetési útmutatója.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4c27895c5163d59ca785aa15fa3739359e5be457
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666606"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>SAP HANA infrastruktúrakonfigurációk és -műveletek az Azure-ban
Ez a dokumentum útmutatást nyújt az Azure-infrastruktúra és az Azure natív virtuális gépeken üzembe helyezett operációs SAP HANA rendszerek konfigurálásához. A dokumentum a M128s virtuális gép SKU-jának SAP HANA kibővíthető konfigurációs adatait is tartalmazza. Ez a dokumentum nem helyettesíti a szabványos SAP-dokumentációt, amely a következő tartalmakat tartalmazza:

- [SAP felügyeleti útmutató](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP telepítési útmutatók](https://service.sap.com/instguides)
- [SAP-megjegyzések](https://service.sap.com/notes)

## <a name="prerequisites"></a>Előfeltételek
Az útmutató használatához a következő Azure-összetevők alapszintű ismerete szükséges:

- [Azure-beli virtuális gépek](../../linux/tutorial-manage-vm.md)
- [Azure-Hálózatkezelés és virtuális hálózatok](../../linux/tutorial-virtual-network.md)
- [Azure Storage](../../linux/tutorial-manage-disks.md)

Az SAP NetWeaver és az Azure-beli SAP-összetevőkkel kapcsolatos további információkért tekintse meg az [Azure dokumentációjának](../../../index.yml) [SAP on Azure](./get-started.md) című szakaszát.

## <a name="basic-setup-considerations"></a>Alapszintű telepítési szempontok
Az alábbi szakaszok a SAP HANA rendszerek Azure-beli virtuális gépeken való üzembe helyezésének alapvető telepítési szempontjait ismertetik.

### <a name="connect-into-azure-virtual-machines"></a>Kapcsolódjon az Azure Virtual Machines szolgáltatáshoz
Az [Azure Virtual Machines tervezési útmutatójában](./planning-guide.md)leírtaknak megfelelően két alapszintű módszer áll rendelkezésre az Azure-beli virtuális gépekhez való csatlakozáshoz:

- Kapcsolódjon az interneten és a nyilvános végpontokon egy Jump VM vagy SAP HANA rendszert futtató virtuális gépen.
- Csatlakozás [VPN](../../../vpn-gateway/tutorial-site-to-site-portal.md) -vagy Azure- [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

A helyek közötti kapcsolat VPN-en vagy ExpressRoute szükséges az éles környezetekben. Az ilyen típusú kapcsolatok olyan nem éles környezetekben is szükségesek, amelyek olyan éles környezetbe kerülnek, ahol az SAP szoftver használatban van. Az alábbi képen egy példa látható a helyek közötti kapcsolatra:

![Helyek közötti kapcsolat](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Azure-beli virtuális gépek típusának kiválasztása
Az éles környezetekben használható Azure-beli virtuálisgép-típusok listáját a [IAAS SAP-dokumentációja](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)tartalmazza. A nem éles környezetekben a natív Azure-beli virtuálisgép-típusok szélesebb választéka érhető el.

>[!NOTE]
> Nem éles környezetekben használja az [SAP-megjegyzés #1928533ban](https://launchpad.support.sap.com/#/notes/1928533)felsorolt virtuálisgép-típusokat. Az Azure-beli virtuális gépek éles környezetekben való használatához tekintse meg a hitelesített virtuális gépek SAP HANA az SAP published [Certified IaaS Platforms listán](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

A virtuális gépek üzembe helyezése az Azure-ban a használatával:

- Az Azure Portal.
- Azure PowerShell parancsmagok.
- Az Azure CLI-vel.

Az [SAP Cloud platformon](https://cal.sap.com/)üzembe helyezhet egy teljes körűen telepített SAP HANA platformot is az Azure-beli virtuálisgép-szolgáltatásokhoz. A telepítési folyamat az [SAP S/4HANA vagy a BW/4HANA üzembe helyezése az Azure](./cal-s4h.md) -ban, illetve az [itt](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)megjelent Automation szolgáltatásban olvasható.

>[!IMPORTANT]
> M208xx_v2 virtuális gépek használatához körültekintően kell kiválasztania a Linux-rendszerképet az Azure VM rendszerkép-katalógusában. A részletek olvasásához olvassa el a [memória-optimalizált virtuális gépek méretét](../../mv2-series.md)ismertető cikket.
> 


### <a name="storage-configuration-for-sap-hana"></a>SAP HANA tárolási konfigurációja
Az Azure-ban SAP HANA használható tárolási konfigurációk és tárolási típusok esetén olvassa el a dokumentumot az [Azure-beli virtuális gépek tárolási konfigurációjának SAP HANA](./hana-vm-operations-storage.md)


### <a name="set-up-azure-virtual-networks"></a>Azure-beli virtuális hálózatok beállítása
Ha a helyek közötti kapcsolatot VPN-en vagy ExpressRoute keresztül csatlakoztatja az Azure-hoz, rendelkeznie kell legalább egy olyan Azure-beli virtuális hálózattal, amely virtuális átjárón keresztül csatlakozik a VPN-vagy ExpressRoute-áramkörhöz. Az egyszerű telepítések során a virtuális átjáró üzembe helyezhető az Azure Virtual Network (VNet) azon alhálózatán is, amely az SAP HANA példányokat is üzemelteti. SAP HANA telepítéséhez két további alhálózatot hoz létre az Azure virtuális hálózaton belül. Az egyik alhálózat tárolja a virtuális gépeket az SAP HANA példányok futtatásához. A másik alhálózat Jumpbox-vagy felügyeleti virtuális gépeket futtat SAP HANA Studio, más felügyeleti szoftver vagy az alkalmazás szoftverének üzemeltetéséhez.

> [!IMPORTANT]
> A funkcionalitás, de még ennél is fontosabb, hogy az [Azure hálózati virtuális berendezések](https://azure.microsoft.com/solutions/network-appliances/) konfigurálása az SAP-alkalmazás és az SAP NetWeaver, Hybris vagy S/4HANA alapú SAP-rendszer adatbázis-kezelő rétege közötti kommunikációs útvonalon nem támogatott. Az SAP-alkalmazás réteg és az adatbázis-kezelő réteg közötti kommunikációnak közvetlennek kell lennie. A korlátozás nem tartalmazza az [Azure ASG és a NSG szabályokat](../../../virtual-network/network-security-groups-overview.md) , amennyiben ezek a ASG-és NSG-szabályok közvetlen kommunikációt tesznek lehetővé. További forgatókönyvek, amelyek esetében a NVA nem támogatottak olyan Azure-beli virtuális gépek közötti kommunikációs útvonalon, amelyek a Linux pacemaker-fürtcsomópontok és a SBD-eszközök [magas rendelkezésre állását mutatják be az Azure-beli virtuális gépeken az SAP NetWeaver SUSE Linux Enterprise Server SAP-alkalmazásokhoz](./high-availability-guide-suse.md) Vagy az Azure-beli virtuális gépek és a Windows Server-SOFS közötti kommunikációs útvonalakban az Azure-beli [fájlmegosztás használatával a Windows feladatátvevő fürtben a fürt SAP ASCS/SCS-példányának](./sap-high-availability-guide-wsfc-file-share.md)beállítása című témakörben leírtak szerint. A kommunikációs elérési utak NVA könnyedén megduplázhatja a két kommunikációs partner közötti hálózati késést, így az SAP-alkalmazási réteg és az adatbázis-kezelő réteg közötti kritikus útvonalakon korlátozhatja az átviteli sebességet. Az ügyfelekkel megfigyelt egyes forgatókönyvekben a NVA a pacemaker linuxos fürtök meghibásodását okozhatják abban az esetben, ha a Linux pacemaker-fürtcsomópontok közötti kommunikációnak egy NVA keresztül kell kommunikálnia a SBD-eszközzel.  
> 

> [!IMPORTANT]
> Egy másik kialakítás, amely **nem** támogatott, az SAP-alkalmazás rétegének és az adatbázis-kezelő rétegnek a különböző Azure- [beli](../../../virtual-network/virtual-network-peering-overview.md) virtuális hálózatokra való elkülönítése, amelyek nincsenek egymással társítva. Azt javasoljuk, hogy a különböző Azure-beli virtuális hálózatok használata helyett az SAP-alkalmazás rétegét és az adatbázis-kezelő réteget az Azure virtuális hálózatban lévő alhálózatok használatával különítse el. Ha úgy dönt, hogy nem követi a javaslatot, és ehelyett elkülöníti a két réteget a különböző virtuális hálózatok között, a két virtuális hálózatnak [egyenrangúnak](../../../virtual-network/virtual-network-peering-overview.md)kell lennie. Vegye figyelembe, hogy a két, egymástól független Azure- [beli](../../../virtual-network/virtual-network-peering-overview.md) virtuális hálózat közötti hálózati forgalom az átvitel költségeinek hatálya alá tartozik. Az SAP-alkalmazási réteg és az adatbázis-kezelő réteg jelentős költségeinek kiváltásával az SAP-alkalmazás rétegében és az adatbázis-kezelő rétegben a nagy mennyiségű adatmennyiség felhalmozható. 

Ha a virtuális gépeket a SAP HANA futtatására telepíti, a virtuális gépeknek a következőkre van szükségük:

- Két virtuális hálózati adapter van telepítve: egy hálózati adaptert a felügyeleti alhálózathoz, és egy hálózati adaptert a helyszíni hálózatról vagy más hálózatokról az Azure-beli virtuális gép SAP HANA példányához való kapcsolódáshoz.
- Statikus magánhálózati IP-címek, amelyek mindkét virtuális hálózati adapterhez telepítve vannak.

> [!NOTE]
> Statikus IP-címeket kell hozzárendelni az Azure-ban az egyes Vnic. Ne rendeljen statikus IP-címeket a vendég operációs rendszeren belül egy vNIC. Bizonyos Azure-szolgáltatások, például a Azure Backup-szolgáltatás arra támaszkodnak, hogy legalább az elsődleges vNIC DHCP-re van beállítva, és nem statikus IP-címekre. Tekintse meg az [Azure-beli virtuális gépek biztonsági mentését](../../../backup/backup-azure-vms-troubleshoot.md#networking)ismertető dokumentumot is. Ha több statikus IP-címet kell hozzárendelni egy virtuális géphez, több Vnic kell hozzárendelnie egy virtuális géphez.
>
>

A tartós üzembe helyezések esetében azonban létre kell hoznia egy virtuális adatközpont-hálózati architektúrát az Azure-ban. Ez az architektúra javasolja az Azure VNet-átjáró elkülönítését, amely a helyszíni környezethez csatlakozik egy különálló Azure-VNet. Ez a különálló VNet minden olyan forgalmat üzemeltet, amely a helyszíni vagy az internet felé irányul. Ez a módszer lehetővé teszi szoftver központi telepítését az Azure-beli virtuális adatközpontba bekerülő forgalom naplózására és naplózására ebben a különálló hub-VNet. Tehát van egy VNet, amely az Azure-beli üzembe helyezéshez kapcsolódó összes szoftvert és konfigurációt üzemelteti.

A cikk az [Azure Virtual Datacenter: a hálózat perspektívája](/azure/architecture/vdc/networking-virtual-datacenter) és [Az Azure Virtual Datacenter és a vállalati vezérlési sík](/azure/architecture/vdc/) további információkat nyújt a virtuális adatközpontok megközelítéséről és a kapcsolódó Azure VNet kialakításáról.


>[!NOTE]
>A hub-VNet és az [Azure VNet](../../../virtual-network/virtual-network-peering-overview.md) -VNet használó küllős közötti forgalom további [költségek](https://azure.microsoft.com/pricing/details/virtual-network/)alá esik. A költségek alapján előfordulhat, hogy meg kell fontolnia, hogy a szigorú elosztó és a küllős hálózati kialakítás futtatása során felmerülő kompromisszumok között a VNet-társítás megkerülése érdekében több [Azure ExpressRoute-átjárót](../../../expressroute/expressroute-about-virtual-network-gateways.md) is futtat, amelyekhez a küllők csatlakoznak. Az Azure ExpressRoute-átjárók azonban további [költségeket](https://azure.microsoft.com/pricing/details/vpn-gateway/) is bevezetnek. A hálózati forgalom naplózásához, naplózásához és figyeléséhez használt harmadik féltől származó szoftverek további költségei is előfordulhatnak. Az adatcsere költségeitől függ az egyoldalas VNet, valamint a további Azure ExpressRoute-átjárók és további szoftverlicenc-eszközök által létrehozott költségektől függően, ha az alhálózatok elkülönítési egységként való használatát választja el a virtuális hálózatok helyett.


Az IP-címek hozzárendelésének különböző módszereiről az [IP-címek típusai és a foglalási módszerek az Azure-ban](../../../virtual-network/public-ip-addresses.md)című cikk nyújt áttekintést. 

SAP HANA rendszert futtató virtuális gépek esetén a hozzárendelt statikus IP-címekkel kell működnie. Ennek oka, hogy a HANA-hivatkozások IP-címeinek néhány konfigurációs attribútuma.

Az [Azure hálózati biztonsági csoportok (NSG-k)](../../../virtual-network/virtual-network-vnet-plan-design-arm.md) az SAP HANA-példányhoz vagy a Jumpbox irányított forgalom irányítására szolgálnak. A NSG és a végül az [alkalmazás biztonsági csoportjai](../../../virtual-network/network-security-groups-overview.md#application-security-groups) a SAP HANA alhálózathoz és a felügyeleti alhálózathoz vannak társítva.

Az alábbi képen egy központi telepítési séma áttekintése látható, amely a hub és a küllős VNet architektúrát követően SAP HANA:

![A SAP HANA durva üzembe helyezési sémája](media/hana-vm-operations/hana-simple-networking-dmz.png)

Ha helyek közötti kapcsolat nélkül szeretné üzembe helyezni SAP HANA az Azure-ban, akkor továbbra is a nyilvános internetről kell elvégeznie a SAP HANA példány védelmét, és el kell rejtenie egy továbbító proxy mögött. Ebben az alapvető forgatókönyvben az üzemelő példány az Azure beépített DNS-szolgáltatásaiban támaszkodik az állomásnevek feloldására. A nyilvános IP-címeket használó összetettebb üzembe helyezésnél különösen fontos az Azure beépített DNS-szolgáltatásai. Az Azure NSG és az [Azure NVA](https://azure.microsoft.com/solutions/network-appliances/) segítségével felügyelheti az internetről érkező útválasztást az Azure-beli Azure VNet-architektúrában. Az alábbi képen egy durva séma látható, amellyel SAP HANA helyezhetők üzembe helyek közötti kapcsolat nélkül egy központhoz és küllő VNet architektúrához:
  
![Durva üzembe helyezési séma SAP HANA helyek közötti kapcsolat nélkül](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

Egy másik leírás arról, hogyan használható az Azure NVA az internetről a hub és a küllő VNet architektúra nélküli hozzáférés vezérlésére és figyelésére a következő cikkben: a [nagymértékben elérhető hálózati virtuális berendezések üzembe helyezése](/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Az Azure-infrastruktúra konfigurálása SAP HANA kibővíthető
Ha meg szeretné tudni, hogy az OLAP-kibővített vagy S/4HANA számára engedélyezett Azure-beli virtuálisgép-típusok kibővítettek-e, ellenőrizze a [SAP HANA hardveres könyvtárat](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). A "fürtözés" oszlop pipa jelzi a kibővített támogatást. Az alkalmazás típusa azt jelzi, hogy támogatott-e az OLAP kibővíthető vagy S/4HANA. Az egyes virtuális gépekre kiterjedő kibővíthető csomópontokkal kapcsolatos részletekért tekintse meg a SAP HANA Hardware könyvtárban felsorolt adott virtuálisgép-SKU bejegyzéseinek részleteit.

A kibővített konfigurációk Azure-beli virtuális gépeken való üzembe helyezéséhez szükséges minimális operációsrendszer-kiadásokat a SAP HANA Hardware könyvtárban található adott virtuálisgép-SKU bejegyzéseinek részleteiben tekintheti meg. Az n-Node OLAP kibővített konfigurációjának egyik csomópontja főcsomópontként működik. A többi csomópont a minősítési műveletek munkavégző csomópontként való korlátozásával. A további készenléti csomópontok nem számítanak bele a hitelesített csomópontok számára.

>[!NOTE]
> A készenléti csomóponttal rendelkező SAP HANA Azure-beli virtuálisgép-kibővítő üzembe helyezése csak a [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) tároló használatával lehetséges. Nincs más SAP HANA minősített Azure Storage SAP HANA készenléti csomópontok konfigurálását teszi lehetővé
>

A/Hana/Shared esetében javasoljuk a [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)használatát is. 

A kibővíthető konfiguráció egyetlen csomópontjának alapszintű kialakítása a következőhöz fog hasonlítani:

![Diagram, amely egy kibővíthető konfiguráció egyetlen csomópontjának általános kialakítását mutatja be.](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

A SAP HANA kibővíthető virtuálisgép-csomópontok alapszintű konfigurációja a következőhöz hasonlóan néz ki:

- A **/Hana/Shared** a Azure NetApp Fileson keresztül biztosított natív NFS szolgáltatást használja. 
- Az összes többi lemez mennyisége nem oszlik meg a különböző csomópontok között, és nem az NFS-en alapul. A nem megosztott **/Hana/Data** és **/Hana/log** rendelkező, kibővíthető HANA-példányok telepítési konfigurációi és lépései a dokumentum későbbi részében is elérhetők. A felhasználható HANA-tárolók esetében tekintse meg a [SAP HANA Azure-beli virtuális gépek tárolási konfigurációit](./hana-vm-operations-storage.md)ismertető cikket.


A kötetek vagy lemezek méretezése érdekében ellenőriznie kell a dokumentumok [SAP HANA a TDI tárolási követelményeit](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), és a munkavégző csomópontok számától függően kell megadnia a szükséges méretet. A dokumentum kibocsátja a kötet szükséges kapacitásának beszerzéséhez szükséges képletet.

A kibővíthető SAP HANA virtuális gépek egycsomópontos konfigurációjának grafikájában megjelenő egyéb kialakítási feltételek a VNet vagy az alhálózat konfigurációjának jobbak. Az SAP kifejezetten azt ajánlja, hogy a HANA-csomópontok közötti kommunikációból elkülönítse az ügyfél/alkalmazás felé irányuló forgalmat. Ahogy az ábrán is látható, ezt a célt úgy érheti el, hogy két különböző Vnic van csatlakoztatva a virtuális géphez. Mindkét Vnic különböző alhálózatokban van, két különböző IP-címmel rendelkezik. Ezután a NSG vagy a felhasználó által megadott útvonalak használatával szabályozhatja az útválasztási szabályok forgalmának áramlását.

Különösen az Azure-ban nincs mód a szolgáltatás minőségének és a kvótáknak az adott Vnic való betartatására. Ennek eredményeképpen az ügyfél/alkalmazás és a csomóponton belüli kommunikáció elkülönítése nem nyit meg semmilyen lehetőséget, hogy az egyik forgalmi adatfolyamot a másikon rangsorolja. Ehelyett a szétválasztás a biztonság mértéke marad a kibővíthető konfigurációk csomóponton belüli kommunikációjának védelmében.  

>[!NOTE]
>Az SAP azt javasolja, hogy a jelen dokumentumban leírtak szerint elkülönítse a hálózati forgalmat az ügyfélre, az alkalmazásra és a csomóponton belüli forgalomra. Ennek megfelelően a rendszer a legutóbbi ábrán látható módon helyez üzembe egy architektúrát. Tekintse meg a biztonsági és megfelelőségi csapatot is a javaslattól eltérő követelmények tekintetében 
>

A hálózati szempontból a minimálisan szükséges hálózati architektúra az alábbihoz hasonló lesz:

![Egyetlen csomópont kibővíthető alapjai](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>SAP HANA kibővíthető n Azure telepítése
A kibővíthető SAP-konfiguráció telepítése során a következők részletes lépéseit kell végrehajtania:

- Új vagy meglévő Azure VNet-infrastruktúra átalakítása
- Az új virtuális gépek üzembe helyezése az Azure Managed Premium Storage, az ultra Disk Volumes és/vagy az NFS-kötetek használatával a ANF alapján
- - Igazítsa a hálózati útválasztást, és győződjön meg róla, hogy például a virtuális gépek közötti csomóponton belüli kommunikáció nem egy [NVA](https://azure.microsoft.com/solutions/network-appliances/)keresztül van átirányítva. 
- Telepítse a SAP HANA fő csomópontot.
- A SAP HANA fő csomópont konfigurációs paramétereinek igazítása
- Folytassa a SAP HANA munkavégző csomópontok telepítésével

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>SAP HANA telepítése kibővíthető konfigurációban
Az Azure-beli virtuálisgép-infrastruktúrák üzembe helyezése és az egyéb előkészületek elvégzése után a következő lépésekben telepítenie kell a SAP HANA kibővítő konfigurációit:

- Az SAP HANA fő csomópont telepítése az SAP dokumentációjának megfelelően
- Ha a/Hana/Data és a/Hana/log nem megosztott lemezekkel rendelkező Azure Premium Storage vagy Ultra Disk Storage-t használ, akkor módosítania kell a global.ini fájlt, és hozzá kell adnia a "basepath_shared = No" paramétert a global.ini fájlhoz. Ez a paraméter lehetővé teszi, hogy a SAP HANA a "Shared" **/Hana/Data** és a csomópontok közötti **/Hana/log** -kötetek nélkül fusson. A részleteket az [SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991)dokumentációja ismerteti. Ha NFS-köteteket használ a/Hana/Data és a/Hana/log ANF alapján, nem kell ezt a módosítást végeznie
- A global.ini paraméterben szereplő esetleges változás után indítsa újra a SAP HANA példányt
- További munkavégző csomópontok hozzáadása. Lásd még: <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html> . Itt adhatja meg a belső hálózatot SAP HANA csomópontok közötti kommunikációhoz a telepítés során, vagy később, például a helyi hdblcm használatával. Részletesebb dokumentáció: [SAP-megjegyzés #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Az SAP HANA kibővíthető rendszernek a SUSE Linux készenléti csomóponttal való beállításával kapcsolatos részletes leírás a [SAP HANA kibővíthető rendszer üzembe helyezése készenléti csomóponttal Azure-beli virtuális gépeken a SUSE Linux Enterprise Server Azure NetApp Files használatával](./sap-hana-scale-out-standby-netapp-files-suse.md)című témakörben található. A Red Hat egyenértékű dokumentációja a következő cikkben található: [SAP HANA kibővíthető rendszer üzembe helyezése készenléti csomóponttal Azure-beli virtuális gépeken Azure NetApp Files használatával Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md). 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>Az Azure Virtual Machines szolgáltatáshoz készült 2,0-es dinamikus előSAP HANA

Az Azure M sorozatú virtuális gépeken SAP HANA minősítéseken kívül SAP HANA a dinamikus rétegek 2,0 is támogatott a Microsoft Azureban (lásd: SAP HANA dinamikus rétegű dokumentációs hivatkozások lejjebb). Habár nem különbözik a termék telepítésének vagy működtetésének, például SAP HANA pilótafülke egy Azure-beli virtuális gépen belül, néhány fontos elem van, amelyek kötelezőek az Azure hivatalos támogatásához. Ezeket a főbb pontokat alább találja. A cikk során a "DT 2,0" rövidítést a rendszer a teljes név, a dinamikus és a 2,0-es érték helyett használni fogja.

SAP BW vagy S4HANA nem támogatja SAP HANA a dinamikus rétegek 2,0ét. A fő használati esetek mostantól natív HANA-alkalmazások.


### <a name="overview"></a>Áttekintés

Az alábbi kép áttekintést nyújt a DT 2,0 támogatásáról Microsoft Azureon. A kötelező követelmények egy készlete, amelyet követni kell a hivatalos minősítés betartása érdekében:

- A DT 2,0-et dedikált Azure-beli virtuális gépre kell telepíteni. Előfordulhat, hogy nem ugyanazon a virtuális gépen fut, amelyen SAP HANA fut
- A SAP HANA és a DT 2,0 virtuális gépeket ugyanazon Azure-vnet belül kell telepíteni
- A SAP HANA és a DT 2,0 virtuális gépeket az Azure gyorsított hálózatkezelés beállítással kell telepíteni
- A DT 2,0 virtuális gépek tárolási típusának Azure-Premium Storage kell lennie
- Több Azure-lemezt kell csatlakoztatni a DT 2,0 virtuális géphez
- A szoftveres RAID/csíkozott kötet (LVM vagy mdadm használatával) létrehozásához az Azure-lemezeken keresztüli csíkozás szükséges.

További részleteket a következő szakaszban talál.

![SAP HANA DT 2,0 architektúrájának áttekintése](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Dedikált Azure-beli virtuális gép SAP HANA DT 2,0

Az Azure IaaS-on a DT 2,0 csak dedikált virtuális gépen támogatott. A DT 2,0-es verzióját nem lehet ugyanazon az Azure-beli virtuális gépen futtatni, amelyen a HANA-példány fut. Kezdetben két virtuálisgép-típust lehet használni SAP HANA DT 2,0-es futtatásához:

- M64-32ms 
- E32sv3 

Lásd a virtuális gép [típusának leírását](../../sizes-memory.md)

A 2,0-es DT alapgondolata miatt a "meleg" adatok kiszervezése a költségek megtakarítása érdekében ésszerű a megfelelő virtuálisgép-méretek használata. A lehetséges kombinációkkal kapcsolatban azonban nincs szigorú szabály. Ez az adott ügyfél munkaterheléstől függ.

Az ajánlott konfigurációk a következőek:

| SAP HANA virtuális gép típusa | DT 2,0 virtuális gép típusa |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


A SAP HANA-tanúsítvánnyal rendelkező M sorozatú virtuális gépek összes kombinációja támogatott DT 2,0 virtuális gépekkel (M64-32ms és E32sv3) lehetséges.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure Networking és SAP HANA DT 2,0

A DT 2,0 dedikált virtuális gépen való telepítéséhez hálózati átviteli sebességre van szükség a DT 2,0 virtuális gép és a SAP HANA 10 GB-os virtuális gép között. Ezért a virtuális gépeket az Azure-vnet belül kell elhelyezni, és engedélyezni kell az Azure gyorsított hálózatkezelését.

Az Azure gyorsított hálózatkezeléssel kapcsolatos további információk [itt](../../../virtual-network/create-vm-accelerated-networking-cli.md) találhatók

### <a name="vm-storage-for-sap-hana-dt-20"></a>VM Storage SAP HANA DT 2,0

A DT 2,0 ajánlott eljárási útmutatója alapján a lemez i/o-átviteli sebességének legalább 50 MB/s-nak kell lennie a fizikai mag esetében. Tekintse meg a két Azure-beli virtuálisgép-típusra vonatkozó SPECT, amelyek a DT 2,0-as verziójával támogatottak

- E32sv3:768 MB/s (nem gyorsítótárazott), ami a fizikai mag 48 MB/s arányát jelenti.
- M64-32ms: 1000 MB/mp (nem gyorsítótárazott), ami a fizikai mag 62,5 MB/s arányát jelenti.

Több Azure-lemez csatlakoztatására van szükség a DT 2,0 virtuális géphez, és az operációs rendszer szintjén létre kell hoznia egy szoftveres RAID-t (csíkozást), hogy a maximálisan engedélyezett legyen a lemez sebessége virtuális gépenként. Egyetlen Azure-lemez nem tudja biztosítani az átviteli sebességet a virtuális gép maximális korlátjának eléréséhez. Az Azure Premium Storage használata kötelező a DT 2,0 futtatásához. 

- Az elérhető Azure-lemezekre vonatkozó részletek [itt](../../disks-types.md) találhatók
- A szoftveres RAID mdadm használatával történő létrehozásával kapcsolatos részletek [itt](/previous-versions/azure/virtual-machines/linux/configure-raid) találhatók
- [Itt](/previous-versions/azure/virtual-machines/linux/configure-lvm) talál részletes információt az LVM konfigurálásáról egy csíkozott kötet létrehozásához a maximális átviteli sebesség érdekében

A méretre vonatkozó követelményektől függően különböző lehetőségek érhetők el egy virtuális gép maximális átviteli sebességének eléréséhez. Az alábbi lehetséges adatmennyiség-lemezes konfigurációk minden DT 2,0 virtuálisgép-típus esetében elérhetők a virtuális gép maximális átviteli sebességének eléréséhez. A E32sv3 virtuális gépet a kisebb munkaterhelések belépési szintjére kell tekinteni. Ha úgy gondolja, hogy nem elég gyors, szükséges lehet a virtuális gép átméretezése a M64-32ms.
Mivel a M64-32ms virtuális gép sok memóriát tartalmaz, előfordulhat, hogy az i/o-terhelés nem éri el a korlátot, különösen az olvasási igényű számítási feladatok esetében. Emiatt a Stripe-készlet kevesebb lemeze is elegendő lehet az ügyfél-specifikus munkaterheléstől függően. A biztonságos oldalon azonban az alábbi lemez-konfigurációk garantálják a maximális átviteli sebességet:


| Virtuális gép termékváltozata | Lemez konfigurációja 1 | Lemez konfigurációja 2 | Lemez konfigurációja 3 | 4. lemez konfigurációja | Lemez konfigurációja 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 – > 16 TB | 4 x P40 – > 8 TB | 5 x P30 – > 5 TB | 7 x P20 – > 3,5 TB | 8 x P15 – 2 TB > | 
| E32sv3 | 3 x P50 – > 12 TB | 3 x P40 – > 6 TB | 4 x P30 – 4 TB > | 5 x P20 – > 2,5 TB | 6 x P15 – > 1,5 TB | 


Különösen abban az esetben, ha a munkaterhelés olvasása intenzív, az i/o-teljesítmény növelése érdekében az Azure-gazdagép gyorsítótárának "írásvédett" értékre való bekapcsolásához ajánlott az adatbázis-szoftverek adatmennyiségének használata. Míg a tranzakciónapló Azure-gazdagépének gyorsítótára "None" lehet. 

A naplózási kötet méretével kapcsolatban javasolt kiindulási pont az adatméret 15%-a. A naplózási kötet létrehozása a költségeket és az átviteli sebességre vonatkozó követelményektől függően különböző Azure-lemezek használatával végezhető el. A naplózási kötet esetében magas I/O-átviteli sebességre van szükség.  A M64-32ms virtuális gép típusának használata esetén a [írásgyorsító](../../how-to-enable-write-accelerator.md)engedélyezéséhez kötelező megadni. Az Azure írásgyorsító a tranzakciós napló optimális lemez-írási késését biztosítja (csak az M sorozat esetében érhető el). Néhány elemet figyelembe kell venni, bár a virtuális gépek maximális száma a virtuálisgép-típusok esetében. A írásgyorsító részletei [itt](../../how-to-enable-write-accelerator.md) találhatók


Íme néhány példa a naplózási kötet méretezésére:

| az adatmennyiség mérete és a lemez típusa | a naplózási kötet és a lemez típusa konfiguráció 1 | naplózási kötet és lemez típusa 2. konfiguráció |
| --- | --- | --- |
| 4 x P50 – > 16 TB | 5 x P20 – > 2,5 TB | 3 x P30 – > 3 TB |
| 6 x P15 – > 1,5 TB | 4 x P6 – > 256 GB | 1 x P15 – > 256 GB |


A SAP HANA skálázáshoz hasonlóan a/Hana/Shared könyvtárat is meg kell osztani a SAP HANA virtuális gép és a DT 2,0 virtuális gép között. Ugyanazt az architektúrát használja, mint a SAP HANA kibővíthető dedikált virtuális gépekkel, amelyek egy magasan elérhető NFS-kiszolgálóként működnek. A megosztott biztonsági mentési kötetek megadásához az azonos kialakítás is használható. Az ügyfél azonban akkor is igénybe vehető, ha szükség lenne rá, vagy ha elég, hogy csak egy dedikált virtuális gépet használjon, amely elegendő tárolókapacitással rendelkezik ahhoz, hogy biztonsági mentési kiszolgálóként működjön.



### <a name="links-to-dt-20-documentation"></a>Hivatkozások a DT 2,0 dokumentációra 

- [SAP HANA a dinamikus rétegek telepítési és frissítési útmutatója](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [A dinamikus rétegek SAP HANA oktatóanyagok és erőforrások](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dinamikus rétegű PoC](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2,0 SPS 02 dinamikus rétegek fejlesztései](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>SAP HANA Azure-beli virtuális gépeken való üzembe helyezésének műveletei
A következő szakaszok ismertetik az Azure-beli virtuális gépeken SAP HANA rendszerek üzembe helyezésével kapcsolatos műveleteket.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure-beli virtuális gépek biztonsági mentésével és visszaállításával kapcsolatos műveletek
A következő dokumentumok azt ismertetik, hogyan lehet biztonsági másolatot készíteni és visszaállítani a SAP HANA üzembe helyezését:

- [SAP HANA biztonsági mentés áttekintése](./sap-hana-backup-guide.md)
- [SAP HANA fájl szintű biztonsági mentés](./sap-hana-backup-file-level.md)
- [SAP HANA tárolási Pillanatképek teljesítményteszte](./sap-hana-backup-guide.md)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>SAP HANAt tartalmazó virtuális gépek elindítása és újraindítása
Az Azure nyilvános felhő Kiemelt funkciója, hogy csak a számítástechnikai percért kell fizetnie. Ha például egy SAP HANA rendszert futtató virtuális gépet állít le, akkor az adott idő alatt csak a tárolási költségekért kell fizetnie. Egy másik funkció akkor érhető el, ha statikus IP-címeket ad meg a virtuális gépekhez a kezdeti üzembe helyezés során. SAP HANAt tartalmazó virtuális gép újraindításakor a virtuális gép a korábbi IP-címeivel indul újra. 


### <a name="use-saprouter-for-sap-remote-support"></a>A SAProuter használata az SAP távoli támogatásához
Ha helyek közötti kapcsolattal rendelkezik a helyszíni helyek és az Azure között, és SAP-összetevőket futtat, akkor valószínűleg már fut a SAProuter. Ebben az esetben végezze el a következő elemeket a távoli támogatáshoz:

- Tartsa karban a SAProuter-konfigurációban SAP HANA üzemeltető virtuális gép magánhálózati és statikus IP-címét.
- Konfigurálja a HANA virtuális gépet üzemeltető alhálózat NSG a 3299-es TCP/IP-porton keresztüli forgalom engedélyezéséhez.

Ha az internetről csatlakozik az Azure-hoz, és nem rendelkezik SAP HANA-vel rendelkező virtuális géphez tartozó SAP-útválasztóval, akkor telepítenie kell az összetevőt. Telepítse a SAProuter-t egy különálló virtuális gépen a felügyeleti alhálózaton. Az alábbi képen egy durva séma látható a SAP HANA helyek közötti kapcsolat és a SAProuter nélküli üzembe helyezéséhez:

![Durva üzembe helyezési séma SAP HANA helyek közötti kapcsolat és SAProuter nélkül](media/hana-vm-operations/hana-simple-networking-saprouter.png)

Ügyeljen arra, hogy a SAProuter külön virtuális gépre telepítse, és ne a Jumpbox virtuális gépén. A különálló virtuális gépnek statikus IP-címmel kell rendelkeznie. A SAProuter az SAP által üzemeltetett SAProuter való összekapcsolásához forduljon az SAP-hoz egy IP-cím használatával. (Az SAP által üzemeltetett SAProuter a virtuális gépre telepített SAProuter-példány párja.) A SAProuter-példány konfigurálásához használja az SAP IP-címét. A konfigurációs beállításokban az egyetlen szükséges port a 3299-as TCP-port.

A SAProuter-en keresztüli távoli támogatási kapcsolatok beállításával és karbantartásával kapcsolatos további információkért tekintse meg az [SAP dokumentációját](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Magas rendelkezésre állás a SAP HANA Azure-beli natív virtuális gépeken
Ha SUSE Linux Enterprise Server vagy Red Hat rendszert futtat, létrehozhat egy pacemaker-fürtöt STONITH-eszközökkel. Az eszközök használatával olyan SAP HANA konfigurációt állíthat be, amely szinkron replikációt használ a HANA rendszerreplikációval és az automatikus feladatátvételsel. További információ a "következő lépések" szakaszban olvasható.

## <a name="next-steps"></a>Következő lépések
Ismerkedjen meg a felsorolt cikkekkel
- [SAP HANA Azure-beli virtuális gépek tárkonfigurációi](./hana-vm-operations-storage.md)
- [SAP HANA kibővíthető rendszer üzembe helyezése készenléti csomóponttal Azure-beli virtuális gépeken Azure NetApp Files használatával SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [SAP HANA kibővíthető rendszer üzembe helyezése készenléti csomóponttal Azure-beli virtuális gépeken Azure NetApp Files használatával Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [SAP HANA magas rendelkezésre állása Azure-beli virtuális gépeken SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
- [SAP HANA magas rendelkezésre állása Azure-beli virtuális gépeken Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)