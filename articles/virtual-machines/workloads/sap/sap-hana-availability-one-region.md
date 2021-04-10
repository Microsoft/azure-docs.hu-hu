---
title: SAP HANA rendelkezésre állás egy Azure-régión belül | Microsoft Docs
description: Ismerteti SAP HANA műveleteket az Azure natív virtuális gépeken egy Azure-régióban.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 845cecfb6b09591b10de30267b31e6e1a80a7482
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504320"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA rendelkezésre állás egy Azure-régión belül
Ez a cikk számos rendelkezésre állási forgatókönyvet ismertet egy Azure-régión belül. Az Azure számos régióval rendelkezik, és a világ minden pontján elterjedt. Az Azure-régiók listájáért lásd: [Azure-régiók](https://azure.microsoft.com/regions/). Az Azure-régión belüli virtuális gépeken való SAP HANA üzembe helyezéséhez a Microsoft egy HANA-példánnyal rendelkező egyetlen virtuális gép üzembe helyezését kínálja. A rendelkezésre állás érdekében két, HANA-példánnyal rendelkező virtuális gépet telepíthet egy olyan Azure-beli rendelkezésre állási [csoporton](../../windows/tutorial-availability-sets.md) belül, amely HANA rendszerreplikációt használ a rendelkezésre álláshoz. 

Az Azure jelenleg [Azure Availability Zones](../../../availability-zones/az-overview.md)kínál. Ez a cikk részletesen ismerteti a Availability Zones. Ez azonban általános vitát tartalmaz a rendelkezésre állási készletek és a Availability Zones használatával kapcsolatban.

Azok az Azure-régiók, amelyekben a Availability Zones több adatközpont is rendelkezésre áll. Az adatközpontok függetlenek az áramforrás, a hűtés és a hálózat kínálatában. A különböző zónák egyetlen Azure-régión belüli felajánlásának oka az alkalmazások telepítése két vagy három Availability Zones. A zónákon belüli üzembe helyezés, az energiagazdálkodással és a hálózatkezeléssel kapcsolatos problémák csak egy Azure-beli rendelkezésre állási zónára épülő infrastruktúrát érintenek, az alkalmazások üzembe helyezése az Azure-régióban Bizonyos kisebb kapacitás előfordulhat. Előfordulhat például, hogy egy zónában lévő virtuális gépek elvesznek, de a másik két zónában lévő virtuális gépek továbbra is futnak. 
 
Az Azure-beli rendelkezésre állási csoport egy logikai csoportosítási funkció, amely segít biztosítani, hogy a rendelkezésre állási csoportban lévő virtuálisgép-erőforrások meghibásodása elkülöníthető legyen egymástól, amikor egy Azure-adatközpontban üzembe helyezik őket. Az Azure biztosítja, hogy a rendelkezésre állási csoportba helyezett virtuális gépek több fizikai kiszolgálón, számítási rackszekrényen, tárolási egységben és hálózati kapcsolón fussanak. Néhány Azure-dokumentációban ezt a konfigurációt a különböző [frissítési és tartalék tartományokban](../../availability.md)lévő elhelyezéseknek nevezzük. Ezek az elhelyezések általában egy Azure-adatközponton belül találhatók. Feltételezve, hogy az áramforrás és a hálózati problémák hatással lennének az üzembe helyezett adatközpontra, az egyik Azure-régióban az összes kapacitása érintett lesz.

Az Azure Availability Zonest jelképező adatközpontok elhelyezése a különböző zónákban üzembe helyezett szolgáltatások és az adatközpontok közötti távolság között fennálló elfogadható hálózati késések miatti kompromisszum. A természeti katasztrófák ideális esetben nem befolyásolják a régió összes Availability Zonesának áramellátását, hálózati kínálatát és infrastruktúráját. Azonban a természetes természeti katasztrófák láthatók, Availability Zones lehet, hogy nem mindig adja meg a rendelkezésre állást, amelyet egyetlen régióban szeretne használni. Gondoljon arra, hogy a Mária hurrikán a Puerto Rico-sziget 2017-es szeptember 20-án eltelik. A Hurricane alapvetően közel 100%-os áramszünetet okozott a 90-mérföld széles szigetén.

## <a name="single-vm-scenario"></a>Egy VM-forgatókönyv

Egy VM-forgatókönyv esetén létre kell hoznia egy Azure-beli virtuális gépet a SAP HANA példányhoz. Az Azure Premium Storage az operációs rendszer lemezének és az összes adatlemezének üzemeltetésére használható. Az Azure rendelkezésre állási SLA-ja 99,9%-os, az egyéb Azure-összetevők SLA-ja pedig elegendő ahhoz, hogy az ügyfelek számára elérhető SLA-kat teljesítse. Ebben az esetben nem kell kihasználnia egy Azure-beli rendelkezésre állási készletet az adatbázis-kezelő réteget futtató virtuális gépek számára. Ebben a forgatókönyvben két különböző funkcióra támaszkodhat:

- Azure-beli virtuális gép automatikus újraindítása (más néven az Azure Service gyógyulása)
- Automatikus újraindítás SAP HANA

Az Azure-beli virtuális gépek automatikus újraindítása vagy szolgáltatás-javító funkciója az Azure-ban két szinten működik:

- Az Azure Server-gazdagép ellenőrzi a kiszolgáló gazdagépén üzemeltetett virtuális gépek állapotát.
- Az Azure Fabric-vezérlő figyeli a kiszolgáló gazdagépének állapotát és rendelkezésre állását.

Az állapot-ellenőrzési funkció figyeli az Azure-kiszolgálón üzemeltetett virtuális gépek állapotát. Ha egy virtuális gép nem Kifogástalan állapotba esik, a virtuális gép újraindítását az Azure-gazdagép ügynöke kezdeményezheti, amely ellenőrzi a virtuális gép állapotát. A háló vezérlő ellenőrzi a gazdagép állapotát úgy, hogy számos különböző paramétert ellenőriz, amelyek a gazdagép hardverével kapcsolatos problémákat jelezhetik. Emellett ellenőrzi a gazdagép elérhetőségét a hálózaton keresztül. A gazdagéptel kapcsolatos problémák jelzése a következő eseményekhez vezethet:

- Ha a gazdagép rossz állapotot jelzi, a rendszer újraindítja a gazdagépet, és újraindítja a gazdagépen futó virtuális gépeket.
- Ha a sikeres újraindítás után a gazdagép nem kifogástalan állapotban van, a rendszer kezdeményezi a virtuális gépek olyan újratelepítését, amelyik eredetileg a nem kifogástalan állapotú csomóponton található. Ebben az esetben az eredeti gazdagép nem kifogástalanként van megjelölve. Nem lesz használatban további központi telepítésekhez, amíg meg nem történik a törlés vagy a lecserélés.
- Ha a nem kifogástalan állapotú gazdagép problémát észlelt az újraindítási folyamat során, a rendszer elindítja a virtuális gépek azonnali újraindítását. 

Az Azure által biztosított gazdagép-és virtuálisgép-figyeléssel az Azure-beli virtuális gépek automatikusan újraindulnak egy kifogástalan Azure-gazdagépen. 

>[!IMPORTANT]
>Az Azure-szolgáltatások gyógyulása nem indítja újra a Linux rendszerű virtuális gépeket, ahol a vendég operációs rendszer kernel pánik állapotban van. A gyakran használt Linux-kiadások alapértelmezett beállításai nem indítják el automatikusan a virtuális gépeket vagy a kiszolgálót, ahol a Linux-kernel pánik állapotban van. Ehelyett az alapértelmezett beállítás szerint az operációs rendszernek a kernel pánik állapotban kell lennie ahhoz, hogy egy kernel-hibakeresőt csatoljon az elemzéshez. Az Azure tiszteletben tartja ezt a viselkedést azáltal, hogy nem indítja automatikusan újra a virtuális gépet egy ilyen állapotban lévő vendég operációs rendszerrel. Feltételezi, hogy az ilyen események rendkívül ritkák. Az alapértelmezett viselkedést felülírhatja a virtuális gép újraindításának engedélyezéséhez. Az alapértelmezett viselkedés módosításához engedélyezze a "kernel. Panic" paramétert a/etc/sysctl.conf. A paraméterhez megadott idő másodpercben. Gyakori ajánlott értékek: várjon 20-30 másodpercig, mielőtt aktiválja az újraindítást ezen a paraméteren. Lásd még: <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf> .

Az ebben a forgatókönyvben használt második funkció az a tény, hogy az újraindított virtuális gépen futó HANA szolgáltatás automatikusan elindul a virtuális gép újraindítása után. A [Hana szolgáltatás automatikus újraindítását](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) a különböző HANA-szolgáltatások watchdog szolgáltatásain keresztül állíthatja be.

Előfordulhat, hogy ezt az egyvirtuális virtuálisgép-forgatókönyvet úgy javítja, hogy egy SAP HANA konfigurációhoz hozzáad egy hideg feladatátvételi csomópontot. A SAP HANA dokumentációjában a telepítő neve [gazdagép automatikus feladatátvétele](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Ez a konfiguráció hasznos lehet egy helyszíni üzembe helyezési helyzetben, ahol a kiszolgáló hardvere korlátozott, és egy egykiszolgálós csomópontot kell kiszolgálni a gazdagép automatikus feladatátvételi csomópontja számára az üzemi gazdagépek készlete számára. Az Azure-ban azonban, ahol az Azure mögöttes infrastruktúrája egy kifogástalan cél kiszolgálót biztosít a virtuális gépek sikeres újraindításához, nem érdemes üzembe helyeznie SAP HANA gazdagép automatikus feladatátvételét. Az Azure Service gyógyulása miatt nincs olyan hivatkozási architektúra, amely a HANA-gazdagép automatikus feladatátvételének készenléti csomópontját előre látja. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Az Azure-SAP HANA kibővíthető konfigurációinak speciális esete
A SAP HANA kibővíthető konfigurációinak magas rendelkezésre állása az Azure-beli virtuális gépek szolgáltatás-gyógyítására és a SAP HANA példány újraindítására támaszkodik, mivel a virtuális gép újra működik. A HANA rendszer Replikálásán alapuló magas rendelkezésre állású architektúrákat később fogjuk bevezetni. 


## <a name="availability-scenarios-for-two-different-vms"></a>Két különböző virtuális gép rendelkezésre állási forgatókönyvei

Ha két Azure-beli virtuális gépet használ egy Azure-beli rendelkezésre állási csoporton belül, megnövelheti a két virtuális gép közötti üzemidőt, ha egy Azure-régióban található Azure-beli rendelkezésre állási csoportba kerülnek. Az Azure-beli alapkonfiguráció a következőképpen fog kinézni:

![Két virtuális gép diagramja az összes réteggel](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

A különböző rendelkezésre állási forgatókönyvek szemléltetése érdekében a diagram néhány rétege ki van hagyva. A diagramon csak a virtuális gépeket, gazdagépeket, rendelkezésre állási csoportokat és az Azure-régiókat ábrázoló rétegek láthatók. Az Azure Virtual Network-példányok, erőforráscsoportok és előfizetések nem játszanak szerepet az ebben a részben ismertetett forgatókönyvekben.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Biztonsági másolatok replikálása egy második virtuális gépre

Az egyik legkezdetlegesebb beállítás a biztonsági másolatok használata. Különösen előfordulhat, hogy az egyik virtuális gépről egy másik Azure-beli virtuális gépre szállított tranzakciós naplóba tartozó biztonsági másolatok vannak. Kiválaszthatja az Azure Storage típusát. Ebben a beállításban az első virtuális gépen a második virtuális gépre irányuló ütemezett biztonsági mentések másolatát kell elvégeznie. Ha a második virtuálisgép-példányt kell használnia, vissza kell állítania a teljes, növekményes/különbözeti és tranzakciós napló biztonsági mentését arra a pontra, amelyre szüksége van. 

Az architektúra a következőképpen néz ki:

![Diagram, amely a tárolási replikációval rendelkező két virtuális gép architektúráját mutatja be.](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Ez a beállítás nem alkalmas a helyreállítási időkorlát (RPO) és a helyreállítási idő célkitűzés (RTO) idejének elérésére. A RTO idő különösen a teljes adatbázisnak a másolt biztonsági másolatok használatával történő teljes visszaállításának szükségessége miatt csökkenhet. Ez a beállítás azonban hasznos lehet a fő példányok nem szándékolt adattörlésének helyreállításához. Ezzel a beállítással bármikor visszaállíthat egy adott időpontra, kinyerheti az adatait, és importálhatja a törölt adatait a fő példányba. Ezért érdemes lehet egy biztonsági másolati módszert használni más magas rendelkezésre állású funkciókkal kombinálva. 

A biztonsági másolatok másolásakor előfordulhat, hogy a SAP HANA példányán futó fő virtuális géppel kisebb virtuális gépet használ. Ne feledje, hogy kisebb számú virtuális merevlemezt is csatolhat kisebb virtuális gépekhez. További információ az egyes virtuálisgép-típusok korlátairól: a [Linux rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Rendszer-replikálás SAP HANA automatikus feladatátvétel nélkül

Az ebben a szakaszban ismertetett forgatókönyvek SAP HANA rendszerreplikációt használnak. Az SAP dokumentációja: [rendszerreplikáció](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Az automatikus feladatátvétel nélküli forgatókönyvek nem általánosak az egyes Azure-régiókban található konfigurációk esetében. Automatikus feladatátvétel nélküli konfiguráció, bár a pacemaker telepítése elkerülhető, ha manuálisan ellenőrzi és feladatátvételt végez. Mivel ez a megoldás és az erőfeszítések is, a legtöbb ügyfél az Azure-szolgáltatások gyógyítására támaszkodik. Vannak olyan Edge-esetek, amikor ez a konfiguráció segíthet a meghibásodási forgatókönyvek szempontjából. Vagy bizonyos esetekben előfordulhat, hogy az ügyfél nagyobb hatékonyságot szeretne megvalósítani.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>SAP HANA rendszer-replikáció automatikus feladatátvétel nélkül és adattovábbítás nélkül

Ebben a forgatókönyvben SAP HANA rendszer-replikálást használ az adatátvitelhez szinkron módon, hogy RPO a 0 értéket. Másfelől azonban elég hosszú RTO, hogy nincs szüksége feladatátvételre vagy az adatelőre való betöltésre a HANA-példány gyorsítótárába. Ebben az esetben lehetséges, hogy a következő műveletek végrehajtásával további gazdaságot érhet el a konfigurációban:

- Futtasson egy másik SAP HANA példányt a második virtuális gépen. A második virtuális gép SAP HANA példánya a virtuális gép memóriáját veszi át. Ha a második virtuális gép feladatátvételét hajtja végre, le kell állítania a futó SAP HANA példányt, amely a második virtuális gépen teljesen betöltődött, így a replikált adatai betölthetők a célként megadott HANA-példány gyorsítótárba a második virtuális gépen.
- Használjon kisebb méretű virtuálisgép-méretet a második virtuális gépen. Ha feladatátvétel történik, további lépésre van szüksége a manuális feladatátvétel előtt. Ebben a lépésben átméretezi a virtuális gépet a forrásként szolgáló virtuális gép méretére. 
 
A forgatókönyv a következőképpen néz ki:

![Két virtuális gép diagramja tárolási replikálással](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Akkor is, ha nem használja a HANA rendszerreplikálási célhelyen a betöltést, legalább 64 GB memóriát kell használnia. Az sortárindex létrehozását-adatoknak a cél példány memóriájában való megtartásához a 64 GB-nál is elegendő memória szükséges.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>SAP HANA rendszer-replikáció automatikus feladatátvétel nélkül és adattovábbítással

Ebben az esetben a második virtuális gépen a HANA-példányba replikált adathalmazok előre be vannak töltve. Ez kiküszöböli a két előnyt az adatvesztés hiányában. Ebben az esetben nem futtathat másik SAP HANA rendszer a második virtuális gépen. Nem használhat kisebb virtuálisgép-méretet is. Ezért az ügyfelek ritkán alkalmazzák ezt a forgatókönyvet.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Rendszer-replikálás SAP HANA automatikus feladatátvételsel

Egy Azure-régióban a standard és a leggyakoribb rendelkezésre állási konfigurációban két SLES Linux rendszerű Azure-beli virtuális gépen van definiálva feladatátvevő fürt. A SLES Linux-fürt a [pacemaker](./high-availability-guide-suse-pacemaker.md) keretrendszeren alapul, a [STONITH](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) -eszközzel együtt. 

SAP HANA szempontból a használt replikációs mód szinkronizálva van, és az automatikus feladatátvétel konfigurálva van. A második virtuális gépen a SAP HANA példány gyors készenléti csomópontként működik. A készenléti csomópont az elsődleges SAP HANA példány változási rekordjainak szinkron streamjét kapja. Mivel az alkalmazás a HANA elsődleges csomóponton hajtja végre a tranzakciókat, az elsődleges HANA-csomópont megvárja, hogy erősítse meg a véglegesítést az alkalmazásnak, amíg a másodlagos SAP HANA csomópont megerősíti, hogy megkapta a véglegesítési rekordot. SAP HANA két szinkron replikációs módot kínál. További részletekért és a két szinkron replikációs mód közötti különbségek leírásához tekintse meg az SAP-cikkben [SAP HANA rendszer replikálásának replikációs módjait](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

A teljes konfiguráció a következőképpen néz ki:

![Két virtuális gép diagramja, tárolási replikálással és feladatátvételsel](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Dönthet úgy, hogy ezt a megoldást választja, mert lehetővé teszi egy RPO = 0 és egy alacsony RTO elérését. Konfigurálja a SAP HANA ügyfélkapcsolatot úgy, hogy a SAP HANA-ügyfelek a virtuális IP-címet használják a HANA rendszerreplikálási konfigurációhoz való csatlakozáshoz. Ilyen konfiguráció esetén nincs szükség az alkalmazás újrakonfigurálására, ha a másodlagos csomópontra feladatátvétel történik. Ebben az esetben az elsődleges és másodlagos virtuális gépekhez tartozó Azure VM SKU-nak azonosnak kell lennie.

## <a name="next-steps"></a>Következő lépések

A konfigurációk Azure-beli beállításával kapcsolatos részletes útmutatásért lásd:

- [Rendszerreplikáció beállítása SAP HANA Azure-beli virtuális gépeken](sap-hana-high-availability.md)
- [SAP HANA magas rendelkezésre állása rendszerreplikáció használatával](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Az Azure-régiók SAP HANA rendelkezésre állásával kapcsolatos további információkért lásd:

- [SAP HANA rendelkezésre állás Azure-régiók között](./sap-hana-availability-across-regions.md)