---
title: A SAP HANA magas rendelkezésre állása és vész-helyreállítása az Azure-ban (nagyméretű példányok) | Microsoft Docs
description: Magas rendelkezésre állás biztosítása és az Azure-beli SAP HANA vész-helyreállításának megtervezése (nagyméretű példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e1fd4d53a642052c55bbe5d31bf80133ae89d35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675462"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Magas rendelkezésre állású és vész-helyreállítási SAP HANA Large Instances az Azure-ban 

>[!IMPORTANT]
>Ez a dokumentáció nem helyettesíti a SAP HANA adminisztrációs dokumentációt vagy az SAP-megjegyzéseket. Várható, hogy az olvasónak alapos ismeretekkel kell foglalkoznia SAP HANA felügyelettel és műveletekkel kapcsolatban, különösen a biztonsági mentés, a visszaállítás, a magas rendelkezésre állás és a vész-helyreállítás témakörében.

Fontos, hogy a környezetében és a HANA verzióiban és kiadásaiban végrehajtott lépéseket és folyamatokat gyakorolja. Az ebben a dokumentációban ismertetett folyamatok egyszerűbben megérthetők, és nem használhatók a végleges működéshez kapcsolódó kézikönyvek részletes lépéseiként. Ha a konfigurációkhoz művelet-kézikönyvek létrehozására van szüksége, tesztelje és gyakorolja a folyamatokat, és dokumentálja az adott konfigurációkhoz kapcsolódó folyamatokat. 


A magas rendelkezésre állás és a vész-helyreállítás (DR) kulcsfontosságú szempont a kritikus fontosságú SAP HANA Azure-beli (nagyméretű példányok) kiszolgálón való futtatásához. Fontos, hogy az SAP-vel, a rendszerintegrátorral vagy a Microsofttal együttműködve megfelelően felkészítse és megvalósítsa a megfelelő magas rendelkezésre állású és vész-helyreállítási stratégiákat. Fontos figyelembe venni a helyreállítási pont célkitűzését (RPO) és a helyreállítási idő célkitűzését is, amelyek a környezetre jellemzőek.

A Microsoft számos SAP HANA magas rendelkezésre állású képességet támogat a HANA nagyméretű példányaival. Ilyen képességek:

- **Tárolási replikáció**: a tárolási rendszerek képesek az összes információ replikálására egy másik, nagyméretű HANA-példányra egy másik Azure-régióban. SAP HANA a metódustól függetlenül működik. Ez a funkció a HANA nagy példányok számára elérhető alapértelmezett vész-helyreállítási mechanizmus.
- **HANA rendszer replikálása**: a [SAP HANAban lévő összes információ replikálása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) egy különálló SAP HANA rendszerbe. A helyreállítási idő célkitűzése az adatreplikáció rendszeres időközönkénti csökkentése. SAP HANA támogatja az aszinkron, szinkron memóriabeli és szinkron üzemmódot. A szinkron mód csak olyan SAP HANA rendszerek esetében használatos, amelyek ugyanabban az adatközpontban találhatók, vagy kevesebb mint 100 km távolságban. A HANA nagyméretű példányainak aktuális kialakításával a HANA rendszer replikálása csak egy régión belül használható magas rendelkezésre álláshoz. A HANA rendszerreplikációhoz egy harmadik féltől származó fordított proxy vagy útválasztási összetevő szükséges a vész-helyreállítási konfigurációkhoz egy másik Azure-régióban. 
- **Gazdagép automatikus feladatátvétele**: egy helyi hibajavítási megoldás, amely a HANA rendszer replikálásának alternatívája SAP HANA. Ha a főcsomópont elérhetetlenné válik, egy vagy több készenléti SAP HANA-csomópontot konfigurálhat kibővített módban, és SAP HANA automatikusan feladatátvételt hajt végre egy készenléti csomóponton.

A SAP HANA az Azure-ban (nagyméretű példányok) négy geopolitikai területen (USA, Ausztrália, Európa és Japán) két Azure-régióban is elérhető. Egy geopolitikai terület két régiója, amely a HANA nagyméretű példányainak tárolására szolgál, külön dedikált hálózati áramkörökhöz csatlakozik. Ezek a tárolási Pillanatképek replikálására szolgálnak a vész-helyreállítási módszerek biztosításához. A replikáció alapértelmezés szerint nem jön létre, de be van állítva a vész-helyreállítási funkciókat megrendelő ügyfelek számára. A tárterület-replikáció a HANA nagyméretű példányaihoz tartozó tárolási Pillanatképek használatától függ. Az Azure-régiót nem lehet olyan DR-régióként választani, amely egy másik geopolitikai területen található. 

A következő táblázat a jelenleg támogatott magas rendelkezésre állású és vész-helyreállítási módszereket és-kombinációkat mutatja be:

| A HANA nagyméretű példányai által támogatott forgatókönyv | Magas rendelkezésre állási beállítás | Vész-helyreállítási beállítás | Megjegyzések |
| --- | --- | --- | --- |
| Egyetlen csomópont | Nem érhető el. | Dedikált DR telepítő.<br /> Többcélú DR-telepítés. | |
| Gazdagép automatikus feladatátvétele: kibővíthető (készenléti állapottal vagy anélkül)<br /> többek között 1 + 1 | Lehetséges, hogy az aktív szerepkört készenléti állapotba helyezi.<br /> A HANA vezérli a szerepkör kapcsolóját. | Dedikált DR telepítő.<br /> Többcélú DR-telepítés.<br /> DR-szinkronizálás a tárolási replikáció használatával. | A HANA-kötetek az összes csomóponthoz vannak csatolva.<br /> A DR-helynek azonos számú csomóponttal kell rendelkeznie. |
| HANA rendszerreplikáció | Az elsődleges vagy másodlagos beállítással lehetséges.<br /> Másodlagos áthelyezés elsődleges szerepkörre egy feladatátvételi esetben.<br /> A HANA rendszerreplikáció és az operációs rendszer vezérlésének feladatátvétele. | Dedikált DR telepítő.<br /> Többcélú DR-telepítés.<br /> DR-szinkronizálás a tárolási replikáció használatával.<br /> A HANA rendszerreplikáció használatával a DR a harmadik féltől származó összetevők nélkül még nem lehetséges. | A kötetek külön készlete van csatolva az egyes csomópontokhoz.<br /> Csak az üzemi helyen található másodlagos replika lemezeit replikálja a rendszer a DR helyre.<br /> A DR helyen a kötetek egyikét kell megadni. | 

Egy dedikált DR-telepítés, ahol a DR hely HANA nagyméretű példánya egysége nem használható más számítási feladatok vagy nem éles környezetek futtatására. Az egység passzív, és csak akkor kerül üzembe, ha vészhelyzeti feladatátvételt hajt végre. Bár ez a beállítás számos ügyfelünk számára nem ajánlott.

Tekintse át a [HLI által támogatott forgatókönyveket](hana-supported-scenario.md) az architektúra tárolási elrendezésének és Ethernet-adatainak megismeréséhez.

> [!NOTE]
> [SAP HANA MCOD](https://launchpad.support.sap.com/#/notes/1681092) üzemelő példányok (több HANA-példány egy egységen) az átfedési forgatókönyvek a táblázatban felsorolt ha és Dr metódusokkal működnek. Kivételt képez a HANA rendszerreplikáció használata egy automatikus feladatátvevő fürttel a pacemaker alapján. Ilyen eset egységenként csak egy HANA-példányt támogat. [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) üzemelő példányok esetében csak a nem Storage-alapú ha-és Dr-metódusok működnek, ha egynél több bérlő van üzembe helyezve. Az egyik bérlő üzembe helyezése esetén a felsorolt metódusok érvényesek.  

A többcélú DR-telepítés, ahol a DR helyen található HANA nagyméretű példány egysége nem éles környezetben üzemelő számítási feladatot futtat. Vészhelyzet esetén állítsa le a nem éles rendszerű rendszerállapotot, csatlakoztassa a Storage-replikált (további) köteteket, majd indítsa el az üzemi HANA-példányt. A HANA nagyméretű példány vész-helyreállítási funkcióit használó ügyfeleink többsége ezt a konfigurációt használja. 


A magas rendelkezésre állásról SAP HANA a következő SAP-cikkekben talál további információt: 

- [SAP HANA magas rendelkezésre állási tanulmány](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA felügyeleti útmutató](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA Academy-videó SAP HANA rendszer-replikációhoz](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP-támogatás Megjegyzés #1999880 – gyakori kérdések SAP HANA rendszer-replikációról](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP-támogatás Megjegyzés #2165547 – SAP HANA biztonsági mentés és visszaállítás SAP HANA rendszer-replikációs környezeten belül](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP-támogatás Megjegyzés #1984882 – SAP HANA rendszerreplikáció használata a hardveres Exchange-hez minimális/nulla állásidővel](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Hálózati megfontolások a nagy méretű HANA-példányokkal való vész-helyreállításhoz

A HANA Large-példányok vész-helyreállítási funkcióinak kihasználásához meg kell terveznie a hálózati kapcsolatot a két Azure-régióval. Szüksége lesz egy Azure ExpressRoute Circuit-kapcsolatra a fő Azure-régióban, és egy másik áramköri kapcsolatot a helyszínen a vész-helyreállítási régióba. Ez a mérték olyan helyzetet mutat be, amelyben probléma merült fel egy Azure-régióban, beleértve a Microsoft Enterprise Edge router (MSEE) helyét is.

Második lépésként összekapcsolhatja az összes olyan Azure-beli virtuális hálózatot, amely az Azure-ban (nagyméretű példányokon) csatlakozik SAP HANAhoz az egyik régióban egy olyan ExpressRoute-áramkörhöz, amely a másik régióban lévő HANA nagyméretű példányokat csatlakoztatja. Ezzel a *kapcsolattal* az 1. régió egy Azure-beli virtuális hálózatán futó szolgáltatások kapcsolódhatnak a 2. régióban található HANA nagyméretű példány-egységekhez, és fordítva. Ez a mérték azt az esetet tárgyalja, amelyben a helyszíni helyhez és az Azure-hoz csatlakozó MSEE-helyek közül csak az egyik kerül offline állapotba.

A következő ábra egy rugalmas konfigurációt mutat be a vész-helyreállítási esetekben:

![Optimális konfiguráció a vész-helyreállításhoz](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Egyéb követelmények a HANA Large instances Storage-replikációval a vész-helyreállításhoz

Az előző, a HANA nagyméretű példányokkal rendelkező helyreállítási beállításokra vonatkozó követelményei mellett a következőket kell tennie:

- Az Azure-ban (nagyméretű példányok) az üzemi SKU-kal megegyező méretű SKU-ket SAP HANA rendelni, és a vész-helyreállítási régióban üzembe helyezheti azokat. Az aktuális ügyfél-telepítésekben ezek a példányok nem éles környezetben futó HANA-példányok futtatására szolgálnak. Ezeket a konfigurációkat nevezzük *többcélú Dr-telepítésnek*.   
- Rendeljen további tárhelyet a DR-webSAP HANA helyhez az Azure-beli (nagyméretű példányok) SKU-ra, amelyet a vész-helyreállítási helyen kíván helyreállítani. A további tárterület vásárlása lehetővé teszi a tárolási kötetek lefoglalását. Lefoglalhatja azokat a köteteket, amelyek az éles környezetbeli Azure-régióból származó tárolási replikálás célját jelentik a vész-helyreállítási Azure-régióban.
- Abban az esetben, ha az elsődleges HSR van beállítva, és Storage-alapú replikációt telepít a DR-helyre, további tárhelyet kell vásárolnia a DR helyen, hogy az elsődleges és a másodlagos csomópontok is replikálva legyenek a DR-helyre.

  **Következő lépések**
- Tekintse át [a biztonsági mentést és a visszaállítást](hana-backup-restore.md).













