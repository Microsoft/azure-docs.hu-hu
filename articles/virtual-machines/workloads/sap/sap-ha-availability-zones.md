---
title: SAP munkaterhelés-konfigurációk Azure Availability Zonesokkal | Microsoft Docs
description: Magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver Azure Availability Zones használatával
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 842c56ef1fb6f68c3d8b82e2633d9a604db9fde2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671636"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>SAP számításifeladat-konfigurációk az Azure Availability Zones szolgáltatással
A különböző SAP Architecture-rétegek Azure-beli rendelkezésre állási csoportokban való üzembe helyezése mellett a legutóbb bevezetett [Azure Availability Zones](../../../availability-zones/az-overview.md) az SAP-alapú számítási feladatokhoz is használható. Az Azure rendelkezésre állási zónája a következőként van definiálva: "egyedi fizikai helyek egy régión belül. Minden zóna egy vagy több, független energiaellátással, hűtéssel és hálózatkezeléssel ellátott adatközpontból tevődik össze. Azure Availability Zones nem érhetők el minden régióban. Az Availability Zonest biztosító Azure-régiók esetében keresse fel az [Azure region térképét](https://azure.microsoft.com/global-infrastructure/geographies/). Ez a térkép megmutatja, hogy mely régiók biztosítanak vagy jelentenek Availability Zones. 

A tipikus SAP NetWeaver-vagy S/4HANA architektúra esetében három különböző réteget kell védelemmel ellátnia:

- SAP-alkalmazás rétege, amely lehet egy-néhány tucat virtuális gép. Azt szeretné, hogy a virtuális gépek ugyanarra a gazdagépre legyenek telepítve. Ezeket a virtuális gépeket az adatbázis-kezelő réteg számára is elfogadható közelségbe kell állítani, hogy a hálózati késés elfogadható ablakban maradjon
- SAP-ASCS/SCS-réteg, amely egyetlen meghibásodási pontot jelképez az SAP NetWeaver és az S/4HANA architektúrában. Általában két olyan virtuális gépet kell megtekinteni, amelyet egy feladatátvételi keretrendszerrel szeretne kiterjedni. Ezért ezeket a virtuális gépeket különböző infrastrukturális hibák és frissítési tartományok számára kell lefoglalni
- SAP adatbázis-kezelő réteg, amely egyetlen meghibásodási pontot jelöl. A szokásos esetekben a feladatátvételi keretrendszer által érintett két virtuális gépről áll. Ezért ezeket a virtuális gépeket különböző infrastrukturális hibák és frissítési tartományok között kell lefoglalni. A kivételek SAP HANA kibővíthető központi telepítések, ahol több mint két virtuális gép is használható

A kritikus virtuális gépek rendelkezésre állási csoportokon vagy Availability Zoneson keresztül történő üzembe helyezésének jelentős különbségei:

- A rendelkezésre állási csoporttal való üzembe helyezés a készleten belüli virtuális gépek összevetése egy zónában vagy adatközpontban (az adott régióra vonatkozik). Ennek eredményeképpen a rendelkezésre állási csoporton keresztül történő üzembe helyezést nem védi olyan energiaellátási, hűtési vagy hálózati problémák, amelyek befolyásolják a zóna dataceter (ka) t. A plusz oldalon a virtuális gépek az adott zónában vagy adatközpontban található frissítési és tartalék tartományokkal vannak igazítva. Kifejezetten az SAP-ASCS vagy az adatbázis-kezelő réteg esetében, ahol a rendelkezésre állási csoport két virtuális gépet véd, a hibák és a frissítési tartományok igazítása megakadályozza, hogy mindkét virtuális gép ugyanazon a gazdagépen legyen kizárva. 
- Virtuális gépek üzembe helyezése egy Azure Availability Zoneson keresztül és különböző zónák kiválasztásával (legfeljebb három lehetséges), a virtuális gépeket a különböző fizikai helyszíneken helyezi üzembe, és a további védelmet biztosít az olyan energiaellátási, hűtési vagy hálózati problémákkal szemben, amelyek befolyásolják a zóna dataceter (ka) t. Ugyanakkor, ha ugyanahhoz a virtuálisgép-családhoz több virtuális gépet helyez üzembe ugyanahhoz a rendelkezésre állási zónához, akkor az ugyanazon a gazdagépen futó virtuális gépekről nem végez védelmet. Ennek eredményeképpen a Availability Zones használatával történő üzembe helyezés ideális az SAP-ASCS és az adatbázis-kezelő réteghez, ahol általában két virtuális gépet vizsgálunk. Az SAP-alkalmazás rétegében, amely a kettőnél több virtuális gép is lehet, lehet, hogy vissza kell térnie egy másik üzemi modellre (lásd később)

A Azure Availability Zones közötti üzembe helyezés motivációja az, hogy az egyetlen kritikus virtuális gép meghibásodását, illetve a szoftveres javítások kritikuson belüli leállásának csökkentését illetően olyan nagyobb infrastruktúra-problémákra van szükség, amelyek hatással lehetnek egy vagy több Azure-adatközpont rendelkezésre állására. 

## <a name="considerations-for-deploying-across-availability-zones"></a>A Availability Zones-ben való üzembe helyezés szempontjai


Availability Zones használatakor vegye figyelembe a következőket:

- Az Azure-régión belül a különböző Availability Zones közötti távolságok nem garantálják a garanciát.
- Availability Zones nem ideális DR-megoldás. A természeti katasztrófák jelentős károkat okozhatnak a globális régiókban, beleértve az energiaellátási infrastruktúrák nagy mennyiségű károsodását is. Előfordulhat, hogy a különböző zónák távolsága nem elég nagy ahhoz, hogy megfelelő DR-megoldást jelentsen.
- A Availability Zones közötti hálózati késés nem azonos az összes Azure-régióban. Bizonyos esetekben az SAP-alkalmazás rétegét különböző zónákon keresztül telepítheti és futtathatja, mert az egyik zónából az aktív adatbázis-kezelő virtuális gépre irányuló hálózati késés elfogadható. Bizonyos Azure-régiókban azonban előfordulhat, hogy az aktív adatbázis-kezelő virtuális gép és az SAP-alkalmazás példánya eltérő zónákban való telepítésekor nem fogadható el az SAP üzleti folyamatok számára. Ezekben az esetekben az üzembe helyezési architektúrának eltérőnek kell lennie az alkalmazás aktív/aktív architektúrája vagy egy olyan aktív/passzív architektúra esetében, ahol a hálózati késés túl magas.
- Ha eldönti, hogy hol kívánja használni a Availability Zones-t, alapozza meg döntését a zónák közötti hálózati késéssel kapcsolatban. A hálózati késés két területen játszik fontos szerepet:
    - A szinkron replikálást igénylő két adatbázis-kezelő példány közötti késés. Minél nagyobb a hálózati késés, annál valószínűbb, hogy a számítási feladatok méretezhetőségét fogja érinteni.
    - A hálózati késések közötti különbség egy olyan virtuális gép között, amely egy, az aktív adatbázis-kezelő példánnyal és egy másik zónában található hasonló virtuális géppel fut a zónában. Mivel ez a különbség növekszik, az üzleti folyamatok és a kötegelt feladatok futási idejének hatása is növekszik, attól függően, hogy a zónában futnak-e az adatbázis-kezelővel vagy egy másik zónában.

Ha Azure-beli virtuális gépeket helyez üzembe Availability Zones és ugyanazon az Azure-régióban található feladatátvételi megoldásokat hoz létre, bizonyos korlátozások érvényesek:

- A Azure Availability Zones üzembe helyezéséhez az [Azure Managed Diskst](https://azure.microsoft.com/services/managed-disks/) kell használnia. 
- A zónák enumerálásának fizikai zónákhoz való hozzárendelése egy Azure-előfizetésen keresztül történik. Ha különböző előfizetéseket használ az SAP-rendszerek üzembe helyezéséhez, minden előfizetéshez meg kell határoznia az ideális zónákat.
- Az Azure-beli rendelkezésre állási csoportok nem helyezhetők üzembe az Azure-beli rendelkezésre állási zónán belül, kivéve, ha az Azure-beli [Proximity](../../co-location.md) Az SAP adatbázis-kezelő réteg és a központi szolgáltatások zónák közötti üzembe helyezésének módja, valamint az SAP-alkalmazás rétegének üzembe helyezése a rendelkezésre állási [csoportokkal](sap-proximity-placement-scenarios.md), valamint a virtuális gépek közelségének biztosítása Ha nem használ Azure Proximity-elhelyezési csoportokat, ki kell választania egyet vagy a másikat a virtuális gépek üzembe helyezési keretrendszereként.
- A Windows Server feladatátvételi fürtszolgáltatás vagy a Linux pacemaker használatával nem használhat [Azure Alapszintű Load Balancer](../../../load-balancer/load-balancer-overview.md) a feladatátvevő fürtre vonatkozó megoldások létrehozásához. Ehelyett az [Azure standard Load BALANCER SKU](../../../load-balancer/load-balancer-standard-availability-zones.md)-t kell használnia.



## <a name="the-ideal-availability-zones-combination"></a>Az ideális Availability Zones kombináció
Ha egy SAP NetWeaver vagy S/4HANA rendszerű zónát szeretne üzembe helyezni, két alapelv közül választhat:

- Aktív/aktív: a ASCS/SCS-t és az adatbázis-kezelő réteget futtató virtuális gépek pár két zónában van elosztva. Az SAP-alkalmazás rétegét futtató virtuális gépek száma ugyanazon a két zónán belül egy páros számra van telepítve. Ha egy adatbázis-kezelő vagy ASCS/SCS virtuális gép feladatátvételt hajt végre, előfordulhat, hogy néhány nyitott és aktív tranzakció vissza lesz állítva. A felhasználók azonban továbbra is bejelentkezve maradnak. Nem igazán számít, hogy a zónák az aktív adatbázis-kezelő virtuális gép és az alkalmazás példányai futnak. Ez az architektúra az előnyben részesített architektúra a zónák közötti üzembe helyezéshez.
- Aktív/passzív: a ASCS/SCS-t futtató virtuális gépek és az adatbázis-kezelő réteget futtató virtuális gépek pár két zónában van elosztva. Az SAP-alkalmazás rétegét futtató virtuális gépek száma a Availability Zones egyikére van telepítve. Az alkalmazás rétegét ugyanabban a zónában futtatja, mint az aktív ASCS/SCS és az adatbázis-kezelő példány. Ezt a telepítési architektúrát akkor használja, ha a különböző zónákon belüli hálózati késés túl magas ahhoz, hogy futtatni lehessen a zónák között elosztott alkalmazási réteget. Ehelyett az SAP-alkalmazás rétegének ugyanabban a zónában kell futnia, mint az aktív ASCS/SCS és/vagy adatbázis-kezelő példány. Ha egy ASCS-vagy SCS-vagy adatbázis-kezelő virtuális gép feladatátvételt hajt végre a másodlagos zónában, akkor nagyobb hálózati késéssel és az átviteli sebesség csökkentésével merülhet fel. És a korábban átadott virtuális gépen a lehető leghamarabb végre kell hajtania a feladatátvételt, hogy vissza lehessen térni az előző adatátviteli szintre. Ha egy zóna leáll, az alkalmazás rétegét feladatátvételt kell végrehajtani a másodlagos zónában. Egy tevékenység, amellyel a felhasználók teljes rendszerleállítást tapasztalnak. Néhány Azure-régióban ez az architektúra az egyetlen életképes architektúra, ha Availability Zonest szeretne használni. Ha nem tudja elfogadni a másodlagos zónába nem tartozó ASCS-/SCS-vagy adatbázis-kezelő virtuális gépek lehetséges hatását, érdemes lehet a rendelkezésre állási csoport üzemelő példányait is jobban megmaradni


Ezért mielőtt eldönti, hogyan használja a Availability Zones, meg kell határoznia a következőket:

- Egy Azure-régió három zónája közötti hálózati késés. A régiók zónái közötti hálózati késés ismerete lehetővé teszi, hogy kiválassza azokat a zónákat, amelyek a legkisebb hálózati késéssel rendelkeznek a zónák közötti hálózati forgalomban.
- A virtuális gép és a virtuális gép közötti késések közötti különbség a választott zónák egyikén belül, és a hálózat késése két különböző zónában.
- Annak megállapítása, hogy a telepítendő virtuálisgép-típusok elérhetők-e a két kiválasztott zónában. Egyes virtuális gépek, különösen az M sorozatú virtuális gépek esetében előfordulhatnak olyan helyzetek, amikor néhány SKU csak kettőben érhető el a három zónában.

## <a name="network-latency-between-and-within-zones"></a>Hálózati késés a zónák és a körzetek között
A különböző zónák közötti késés megállapításához a következőket kell tennie:

- Telepítse a virtuális gép SKU-t, amelyet az adatbázis-kezelő példányhoz kíván használni mindhárom zónában. Győződjön meg arról, hogy az [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van a mérés során.
- Ha megtalálta a legkisebb hálózati késéssel rendelkező két zónát, helyezzen üzembe egy másik három virtuális GÉPET, amelyet az alkalmazás rétegbeli virtuális gépként kíván használni a három Availability Zones. Mérje meg a hálózati késleltetést a két adatbázis-kezelő virtuális gépen a kiválasztott két adatbázis-kezelő zónában. 
- Használat **`niping`** mérési eszközként. Az SAP-től származó eszközt az SAP-támogatási megjegyzések [#500235](https://launchpad.support.sap.com/#/notes/500235) és [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)című témakör írja le. Koncentráljon a késési mérésekhez dokumentált parancsokra. Mivel a **ping** nem működik az Azure gyorsított hálózati kód elérési útjain, nem ajánlott használni.

Ezeket a teszteket nem kell manuálisan végrehajtania. Megtalálhatja a PowerShell-eljárás [rendelkezésre állási zónájának késési tesztjét](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) , amely automatizálja a leírt késési teszteket. 

A mérések és a virtuális gépek Availability Zonesban való rendelkezésre állása alapján bizonyos döntéseket kell meghoznia:

- Határozza meg az adatbázis-kezelő réteg ideális zónáit.
- Döntse el, hogy az aktív SAP-alkalmazás rétegét egy, kettő vagy mindhárom zónában szeretné-e terjeszteni a zóna hálózati késése és a zónák közötti különbségek alapján.
- Döntse el, hogy aktív/passzív konfigurációt vagy aktív/aktív konfigurációt szeretne üzembe helyezni egy alkalmazási pontból. (Ezeket a konfigurációkat a cikk későbbi részében ismertetjük.)

A döntések meghozatala során az SAP hálózati késéssel kapcsolatos javaslatait is figyelembe kell vennie az SAP-Megjegyzés [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)dokumentációjában leírtaknak megfelelően.

> [!IMPORTANT]
> A mérések és döntések érvényesek a mérések során használt Azure-előfizetésre. Ha másik Azure-előfizetést használ, meg kell ismételnie a mértékeket. A enumerált zónák leképezése más Azure-előfizetések esetében eltérő lehet.


> [!IMPORTANT]
> A korábban ismertetett mérések várhatóan különböző eredményeket biztosítanak minden olyan Azure-régióban, amely támogatja a [Availability Zones](https://azure.microsoft.com/global-infrastructure/geographies/). Ha a hálózati késésre vonatkozó követelmények azonosak, előfordulhat, hogy különböző központi telepítési stratégiákat kell elfogadnia különböző Azure-régiókban, mert a zónák közötti hálózati késés eltérő lehet. Egyes Azure-régiókban a három különböző zóna hálózati késése rendkívül eltérő lehet. Más régiókban a három különböző zóna közötti hálózati késés nagyobb egység lehet. Az a jogcím, hogy mindig van egy 1 és 2 ezredmásodperc közötti hálózati késés, nem megfelelő. Az Azure-régiók Availability Zones közötti hálózati késés nem általánosítható.

## <a name="activeactive-deployment"></a>Aktív/aktív üzembe helyezés
Az üzembe helyezési architektúra neve aktív/aktív, mert az aktív SAP-alkalmazás-kiszolgálókat két vagy három zónában helyezi üzembe. A sorba helyezni replikálást használó SAP központi szolgáltatási példány két zóna között lesz telepítve. Ugyanez vonatkozik az adatbázis-kezelő rétegre is, amely az SAP Central szolgáltatással megegyező zónákban lesz telepítve. Ebben a konfigurációban meg kell keresnie a régióban a két Availability Zones, amely a munkaterheléshez és a szinkron adatbázis-kezelői replikáláshoz elfogadható, zónák közötti hálózati késést kínál. Győződjön meg arról is, hogy a kiválasztott zónákon belüli hálózati késés és a zóna közötti hálózati késés nem túl nagy. 

Az SAP-architektúra jellegéből adódóan, ha másképp konfigurálja, a felhasználók és a Batch-feladatok a különböző alkalmazás-példányokban hajthatók végre. Ennek a ténynek az aktív/aktív üzembe helyezésével kapcsolatos mellékhatása, hogy a Batch-feladatokat bármely olyan SAP-alkalmazási példány hajthatja végre, amely független az adott zónában lévő, aktív adatbázis-kezelővel vagy sem. Ha a különbségi zónák közötti különbség a zónán belüli hálózati késéshez képest kicsi, akkor előfordulhat, hogy a Batch-feladatok futási idejének eltérése nem jelentős. Ha azonban nagyobb a hálózati késés a zónán belüli hálózati forgalomhoz képest, a kötegelt feladatok futási ideje nagyobb hatással lehet, ha a feladat olyan zónában lett végrehajtva, amelyben az adatbázis-kezelő példány nem aktív. Ön, mint ügyfél, hogy eldöntse, milyen eltérések állnak fenn a futási időben. És azzal, hogy milyen a tolerálható hálózati késés a több zóna forgalmát illetően.

Az olyan Azure-régiók, amelyekben az aktív/aktív üzembe helyezés nem lehet jelentős eltérés a futási idő és az átviteli sebesség között a különböző Availability Zonesokban üzembe helyezett alkalmazási rétegben, például:

- West RÉGIÓJA (mindhárom zóna)
- Kelet-RÉGIÓJA (mindhárom zóna)
- USA középső régiója (mindhárom zóna)
- Észak-Európa (mindhárom zóna)
- Nyugat-Európa (a három zóna közül kettő)
- USA keleti régiója (a három zóna közül kettő)
- USA déli középső régiója (a három zóna közül kettő)
- Egyesült Királyság déli régiója (a három zóna közül kettő)

Azok az Azure-régiók, amelyekben az SAP központi telepítési architektúrája a zónák között nem ajánlott:

- Közép-Franciaország 
- Dél-Afrika északi régiója
- Közép-Kanada
- Kelet-Japán

Attól függően, hogy mit szeretne elviselni a futási idő eltérései között, a felsorolt régiók is érvényesek lehetnek.


A két zónába tartozó aktív/aktív telepítés egyszerűsített sémája a következőképpen néz ki:

![Aktív/aktív zóna üzembe helyezése](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Ehhez a konfigurációhoz a következő szempontokat kell figyelembe venni:

- Ha nem használja az [Azure Proximity](../../co-location.md)beosztási csoportot, akkor a Azure Availability Zones az összes virtuális gép meghibásodási és frissítési tartománya kezeli, mivel a rendelkezésre állási csoportok nem helyezhetők üzembe Azure Availability Zones.
- Ha az adatbázis-kezelő réteg és a központi szolgáltatások zónákhoz való központi telepítését szeretné kombinálni, de az alkalmazási réteghez szeretné használni az Azure-beli rendelkezésre állási csoportokat, akkor az Azure Proximity-elhelyezési csoportok című cikkben leírtak szerint Azure közelségi csoportokat kell használnia az [SAP-alkalmazásokkal való optimális hálózati késés](sap-proximity-placement-scenarios.md)érdekében.
- Az SAP Central Services és az adatbázis-kezelő réteg feladatátvevő fürtjének terheléselosztó esetén a [szabványos SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)kell használnia. Az alapszintű Load Balancer nem fog működni a zónák között.
- Az SAP-rendszer üzemeltetéséhez üzembe helyezett Azure-beli virtuális hálózat az alhálózatokkal együtt a zónák között van kiterjesztve. Minden zónához nincs szükség külön virtuális hálózatokra.
- Az összes telepített virtuális gép esetében az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)-t kell használnia. A nem felügyelt lemezek nem támogatottak a zónákon üzemelő példányok esetében.
- Az Azure Premium Storage, [Ultra SSD Storage](../../disks-types.md#ultra-disk)vagy a ANF nem támogatja a zónák közötti bármilyen típusú replikálást. Az alkalmazásnak (adatbázis-kezelői vagy SAP központi szolgáltatásoknak) replikálnia kell a fontos adatforrásokat.
- Ugyanez érvényes a megosztott sapmnt könyvtárra, amely egy megosztott lemez (Windows), egy CIFS-megosztás (Windows) vagy egy NFS-megosztás (Linux). Olyan technológiát kell használnia, amely replikálja ezeket a megosztott lemezeket vagy megosztásokat a zónák között. Ezek a technológiák támogatottak:
  - Windows rendszer esetén a SIOS DataKeeper használó fürtözött megoldás a [Windows feladatátvevő fürtön lévő SAP ASCS/SCS-példányok fürtözött megosztott lemezzel való használatával, az Azure-ban](./sap-high-availability-guide-wsfc-shared-disk.md)dokumentálva.
  - SUSE Linux esetében az NFS-megosztások, amelyek a [magas rendelkezésre állású NFS](./high-availability-guide-suse-nfs.md)-ben, a SUSE Linux Enterprise Server-on található Azure-beli virtuális gépeken.
    
    A Microsoft Scale-Out fájlkiszolgálót használó megoldás jelenleg a [Windows feladatátvevő fürt és az SAP ASCS/SCS-példányok esetében az Azure-infrastruktúra előkészítése a magas rendelkezésre álláshoz](./sap-high-availability-infrastructure-wsfc-file-share.md)című cikkben leírtaknak megfelelően nem támogatott a zónák között.
- A harmadik zóna a SBD eszköz futtatására szolgál, ha [SUSE Linux pacemaker-fürtöt](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) hoz létre, és a SBD-eszközöket használja az Azure vívó-ügynök helyett. Vagy további alkalmazás-példányok esetén.
- A kritikus fontosságú üzleti folyamatok futtatási időkonzisztenciájának eléréséhez próbáljon meg bizonyos batch-feladatokat és felhasználókat olyan alkalmazás-példányokhoz irányítani, amelyek az aktív adatbázis-kezelői példánnyal rendelkeznek az SAP batch Server-csoportok, az SAP-bejelentkezési csoportok vagy az RFC-csoportok használatával. A zónák közötti feladatátvétel esetén azonban ezeket a csoportokat manuálisan kell áthelyeznie az aktív adatbázis-virtuálisgép-zónán belüli virtuális gépeken futó példányokra.  
- Előfordulhat, hogy minden zónában érdemes lehet alvó párbeszédpanel-példányokat telepíteni. 

> [!IMPORTANT]
> Ebben az aktív/aktív forgatókönyvben a Microsoft a 04/01/2020-es verzióban további díjakat jelent a sávszélességgel kapcsolatban. Olvassa el a dokumentum [sávszélesség-díjszabását](https://azure.microsoft.com/pricing/details/bandwidth/). Az SAP-alkalmazás réteg és az SAP adatbázis-kezelő réteg közötti adatátvitel meglehetősen intenzív. Ezért az aktív/aktív forgatókönyvek nagy mértékben hozzájárulhatnak a költségekhez. A részletes költségek beszerzéséhez tekintse meg a cikk ellenőrzését 


## <a name="activepassive-deployment"></a>Aktív/passzív üzembe helyezés
Ha nem talál elfogadható különbözetet az egyik zónán belüli hálózati késés és a zónák közötti hálózati forgalom késése között, olyan architektúrát helyezhet üzembe, amely aktív/passzív karaktert tartalmaz az SAP-alkalmazás rétegének nézetében. Definiáljon egy *aktív* zónát, amely az a zóna, amelyben a teljes alkalmazás réteget üzembe helyezi, és ahol megkísérli az aktív adatbázis-kezelő és az SAP központi szolgáltatások példányának futtatását. Ilyen konfiguráció esetén meg kell győződnie arról, hogy nem rendelkezik a szélsőséges futásidejű változatokkal, attól függően, hogy a feladat a zónában fut-e az aktív adatbázis-kezelői példánnyal, vagy sem, az üzleti tranzakciókban és a kötegelt feladatokban.

Azok az Azure-régiók, amelyekben az ilyen típusú telepítési architektúra különböző zónákban előnyös lehet:

- Délkelet-Ázsia
- Kelet-Ausztrália
- Dél-Brazília
- Középnyugat-Németország
- Dél-Afrika északi régiója
- Közép-Franciaország 
- Közép-Kanada
- Kelet-Japán


Az architektúra alapszintű elrendezése a következőképpen néz ki:

![Aktív/passzív zónák üzembe helyezése](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Ehhez a konfigurációhoz a következő szempontokat kell figyelembe venni:

- A rendelkezésre állási készletek nem helyezhetők üzembe Azure Availability Zonesban. Ennek kompenzálására az Azure Proximity-elhelyezési csoportok az Azure Proximity-elhelyezési csoportok című cikkben ismertetett módon használhatók az [SAP-alkalmazásokkal való optimális hálózati késés](sap-proximity-placement-scenarios.md)érdekében.
- Ha ezt az architektúrát használja, szorosan figyelnie kell az állapotot, és meg kell őriznie az aktív adatbázis-kezelő és az SAP központi szolgáltatások példányait ugyanabban a zónában, mint a telepített alkalmazási réteget. Az SAP Central szolgáltatás vagy az adatbázis-kezelő példány feladatátvétele esetén győződjön meg arról, hogy a lehető leggyorsabban üzembe helyezett SAP-alkalmazás réteggel manuálisan tud visszaadni a zónába.
- Az SAP Central Services és az adatbázis-kezelő réteg feladatátvevő fürtjének terheléselosztó esetén a [szabványos SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)kell használnia. Az alapszintű Load Balancer nem fog működni a zónák között.
- Az SAP-rendszer üzemeltetéséhez üzembe helyezett Azure-beli virtuális hálózat az alhálózatokkal együtt a zónák között van kiterjesztve. Minden zónához nincs szükség külön virtuális hálózatokra.
- Az összes telepített virtuális gép esetében az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)-t kell használnia. A nem felügyelt lemezek nem támogatottak a zónákon üzemelő példányok esetében.
- Az Azure Premium Storage, [Ultra SSD Storage](../../disks-types.md#ultra-disk)vagy a ANF nem támogatja a zónák közötti bármilyen típusú replikálást. Az alkalmazásnak (adatbázis-kezelői vagy SAP központi szolgáltatásoknak) replikálnia kell a fontos adatforrásokat.
- Ugyanez érvényes a megosztott sapmnt könyvtárra, amely egy megosztott lemez (Windows), egy CIFS-megosztás (Windows) vagy egy NFS-megosztás (Linux). Olyan technológiát kell használnia, amely replikálja ezeket a megosztott lemezeket vagy megosztásokat a zónák között. Ezek a technológiák támogatottak:
    - Windows rendszer esetén a SIOS DataKeeper használó fürtözött megoldás a [Windows feladatátvevő fürtön lévő SAP ASCS/SCS-példányok fürtözött megosztott lemezzel való használatával, az Azure-ban](./sap-high-availability-guide-wsfc-shared-disk.md)dokumentálva.
    - SUSE Linux esetében az NFS-megosztások, amelyek a [magas rendelkezésre állású NFS](./high-availability-guide-suse-nfs.md)-ben, a SUSE Linux Enterprise Server-on található Azure-beli virtuális gépeken.
    
  A Microsoft Scale-Out fájlkiszolgálót használó megoldás jelenleg a [Windows feladatátvevő fürt és az SAP ASCS/SCS-példányok esetében az Azure-infrastruktúra előkészítése a magas rendelkezésre álláshoz](./sap-high-availability-infrastructure-wsfc-file-share.md)című cikkben leírtaknak megfelelően nem támogatott a zónák között.
- A harmadik zóna a SBD eszköz futtatására szolgál, ha [SUSE Linux pacemaker-fürtöt](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) hoz létre, és a SBD-eszközöket használja az Azure vívó-ügynök helyett. Vagy további alkalmazás-példányok esetén.
- A alvó állapotban lévő virtuális gépeket a passzív zónában (az adatbázis-kezelői nézetből) kell üzembe helyeznie, így a zóna meghibásodása esetén elindíthatja az alkalmazás erőforrásait.
    - [Azure site Recovery](https://azure.microsoft.com/services/site-recovery/) jelenleg nem tudja replikálni az aktív virtuális gépeket a zónák közötti, alvó állapotban lévő virtuális gépekre. 
- Érdemes olyan automatizálást befektetni, amely lehetővé teszi, hogy automatikusan elindítsa az SAP-alkalmazás rétegét a második zónában, ha a zóna meghibásodása leáll.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>A magas rendelkezésre állás és a vész-helyreállítási konfiguráció együttese
A Microsoft nem osztja meg az Azure-régió különböző Azure Availability Zoneseit üzemeltető létesítmények földrajzi távolságával kapcsolatos információkat. Egyes ügyfelek azonban egy kombinált HA-és DR-konfiguráció zónáit használják, amelyek nulla értékű helyreállítási időkorlátot (RPO) ígérnek. A RPO nulla azt jelenti, hogy a vész-helyreállítás esetén sem kell elveszíteni a véglegesített adatbázis-tranzakciókat. 

> [!NOTE]
> Javasoljuk, hogy csak bizonyos esetekben használjon konfigurációt. Előfordulhat például, hogy az Azure-régió biztonsági vagy megfelelőségi okokból nem hagyhatják el az adatközpontot. 

Íme egy példa arra, hogyan néz ki egy ilyen konfiguráció:

![Kombinált magas rendelkezésre állású DR zónákban](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Ehhez a konfigurációhoz a következő szempontokat kell figyelembe venni:

- Feltételezi, hogy jelentős távolság van a rendelkezésre állási zónát üzemeltető létesítmények között, vagy ha egy bizonyos Azure-régióban szeretne maradni. A rendelkezésre állási készletek nem helyezhetők üzembe Azure Availability Zonesban. Ennek kompenzálására az Azure Proximity-elhelyezési csoportok az Azure Proximity-elhelyezési csoportok című cikkben ismertetett módon használhatók az [SAP-alkalmazásokkal való optimális hálózati késés](sap-proximity-placement-scenarios.md)érdekében.
- Ha ezt az architektúrát használja, szorosan figyelnie kell az állapotot, és meg kell őriznie az aktív adatbázis-kezelő és az SAP központi szolgáltatások példányait ugyanabban a zónában, mint a telepített alkalmazási réteget. Az SAP Central szolgáltatás vagy az adatbázis-kezelő példány feladatátvétele esetén győződjön meg arról, hogy a lehető leggyorsabban üzembe helyezett SAP-alkalmazás réteggel manuálisan tud visszaadni a zónába.
- Az aktív QA alkalmazás példányait futtató virtuális gépeken előre telepítve kell lennie az éles üzemben lévő példányoknak.
- Egy zónabeli meghibásodás esetén állítsa le a QA-alkalmazás példányait, és indítsa el helyette az éles példányokat. Ehhez a munkához virtuális neveket kell használnia az alkalmazás példányaihoz.
- Az SAP Central Services és az adatbázis-kezelő réteg feladatátvevő fürtjének terheléselosztó esetén a [szabványos SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)kell használnia. Az alapszintű Load Balancer nem fog működni a zónák között.
- Az SAP-rendszer üzemeltetéséhez üzembe helyezett Azure-beli virtuális hálózat az alhálózatokkal együtt a zónák között van kiterjesztve. Minden zónához nincs szükség külön virtuális hálózatokra.
- Az összes telepített virtuális gép esetében az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)-t kell használnia. A nem felügyelt lemezek nem támogatottak a zónákon üzemelő példányok esetében.
- Az Azure Premium Storage és a [Ultra SSD Storage](../../disks-types.md#ultra-disk) nem támogatja a zónák közötti bármilyen típusú replikációt. Az alkalmazásnak (adatbázis-kezelői vagy SAP központi szolgáltatásoknak) replikálnia kell a fontos adatforrásokat.
- Ugyanez érvényes a megosztott sapmnt könyvtárra, amely egy megosztott lemez (Windows), egy CIFS-megosztás (Windows) vagy egy NFS-megosztás (Linux). Olyan technológiát kell használnia, amely replikálja ezeket a megosztott lemezeket vagy megosztásokat a zónák között. Ezek a technológiák támogatottak:
    - Windows rendszer esetén a SIOS DataKeeper használó fürtözött megoldás a [Windows feladatátvevő fürtön lévő SAP ASCS/SCS-példányok fürtözött megosztott lemezzel való használatával, az Azure-ban](./sap-high-availability-guide-wsfc-shared-disk.md)dokumentálva.
    - SUSE Linux esetében az NFS-megosztások, amelyek a [magas rendelkezésre állású NFS](./high-availability-guide-suse-nfs.md)-ben, a SUSE Linux Enterprise Server-on található Azure-beli virtuális gépeken.

  A Microsoft Scale-Out fájlkiszolgálót használó megoldás jelenleg a [Windows feladatátvevő fürt és az SAP ASCS/SCS-példányok esetében az Azure-infrastruktúra előkészítése a magas rendelkezésre álláshoz](./sap-high-availability-infrastructure-wsfc-file-share.md)című cikkben leírtaknak megfelelően nem támogatott a zónák között.
- A harmadik zóna a SBD eszköz futtatására szolgál, ha [SUSE Linux pacemaker-fürtöt](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) hoz létre, és a SBD-eszközöket használja az Azure vívó-ügynök helyett. 





## <a name="next-steps"></a>Következő lépések
Íme néhány további lépés a Azure Availability Zones üzembe helyezéséhez:

- [Az SAP ASCS/SCS-példányok fürtözése Windows feladatátvevő fürtön az Azure-ban megosztott fürtözött lemez használatával](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Azure-infrastruktúra előkészítése az SAP magas rendelkezésre állásához Windows feladatátvevő fürt és fájlmegosztás használatával SAP ASCS/SCS-példányok esetén](./sap-high-availability-infrastructure-wsfc-file-share.md)