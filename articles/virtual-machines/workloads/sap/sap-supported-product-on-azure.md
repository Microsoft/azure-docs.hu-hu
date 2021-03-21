---
title: 'SAP az Azure-on: milyen SAP-szoftverek támogatottak az Azure-ban'
description: Ismerteti, hogy milyen SAP-szoftverek telepíthetők az Azure-ban
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/12/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dfd560754d92c97b435ff7643f6da4d3e62e8cfd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673529"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Milyen SAP-szoftvert támogatnak az Azure-beli üzemelő példányok?
Ez a cikk azt ismerteti, hogyan tudhatja meg, hogy milyen SAP-szoftverek támogatottak az Azure-környezetekben, valamint hogy a szükséges operációs rendszerek és az adatbázis-kezelők milyen kiadásokkal rendelkeznek.

Annak kiértékelése, hogy a jelenlegi SAP-szoftver támogatott-e, és hogy az Azure-beli SAP-szoftverek milyen operációsrendszer-és adatbázis-verziókat támogatnak, a következőkre lesz szüksége:

- SAP-támogatási megjegyzések
- SAP-termék rendelkezésre állási mátrixa



## <a name="general-restrictions-for-sap-workload"></a>Az SAP számítási feladatok általános korlátozásai
Az SAP-munkaterhelésekhez használható Azure IaaS-szolgáltatások x86-64 vagy x64-es hardverre korlátozódnak. Nincs olyan SPARC vagy Power CPU-alapú ajánlat, amely az SAP-munkaterhelésre vonatkozik. Azok az ügyfelek, akik a hardveres architektúrák, például az IBM mainframe vagy a AS400, illetve a HP-UX, a Solaris vagy az AIX operációs rendszerek által használt operációs rendszereken futnak, a következő operációs rendszerek egyikére kell módosítaniuk az SAP-alkalmazásaikat, például az adatbázis-kezelő rendszereket:

- Windows Server 64bit az x86-64 platformhoz
- SUSE Linux 64bit az x86-64 platformhoz
- Red Hat Linux 64Bit az x86-64 platformhoz
- Oracle Linux 64bit az x86-64 platformhoz

Az SAP szoftverrel együtt nem támogatott más operációsrendszer-kiadás vagy Linux-disztribúció. Az egyes verziók és esetek pontos részleteit a dokumentum későbbi részében dokumentálja.


## <a name="you-start-here"></a>Itt indul
A kiindulási pont az [SAP-támogatási megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533). Ahogy az SAP-jegyzetet felülről lefelé haladva, a támogatott szoftverek és virtuális gépek számos területe látható

Az első szakasz az Azure-beli virtuális gépeken az SAP-szoftverrel támogatott operációs kiadásokra vonatkozó minimális követelményeket sorolja fel. Ha nem éri el ezeket a minimális követelményeket, és ezen operációs rendszerek régebbi verzióit futtatja, frissítenie kell az operációs rendszer kiadását egy ilyen minimális kiadásra vagy még újabb kiadásokra. Helyes, hogy az Azure általánosságban támogatni fogja az egyes operációs rendszerek régebbi kiadásait. A felsorolt korlátozások vagy minimális kiadások azonban a végrehajtott tesztek és képesítések alapján futnak, és nem lesznek tovább bővítve. 


> [!NOTE]
>Vannak bizonyos virtuálisgép-típusok, HANA nagyméretű példányok vagy SAP-munkaterhelések, amelyeken újabb operációsrendszer-kiadásokra lesz szükség. A dokumentumhoz hasonló példákat is megemlítünk. Például az SAP-megjegyzésekben vagy más SAP-kiadványokban egyértelműen dokumentált esetekben.

A következő szakasz azokat a általános SAP-platformokat sorolja fel, amelyek támogatottak a támogatott kiadásokban, és ami még fontosabb a támogatott SAP-kernelek esetében. A által támogatott NetWeaver/ABAP vagy Java stackeket listázza, amelyek minimális kernel-kiadásokat igényelnek. Az Azure-ban a legújabb ABAP-stackek támogatottak, de nem szükségesek a minimális kernel-kiadások, mivel az Azure-beli módosítások a legújabb stackek fejlesztésének elejéről lettek megvalósítva

A következőket kell ellenőriznie:

- Azt határozza meg, hogy a futtatott SAP-alkalmazások a megadott minimális kiadások hatálya alá tartoznak-e. Ha nem, meg kell határoznia egy új cél kiadást, az SAP-termékek rendelkezésre állási mátrixában kell megadnia, hogy milyen operációsrendszer-buildek és adatbázis-kezelői kombinációk támogatottak az új célként megadott kiadásban. Így kiválaszthatja az operációs rendszer és az adatbázis-kezelő megfelelő kiadását
- Szükség van-e az SAP-kernelek frissítésére az Azure-ba való áttéréskor
- Azt határozza meg, hogy frissíteni kell-e az SAP-támogatási csomagokat. Különösen olyan támogatási csomagok, amelyek szükségesek azokhoz az esetekhez, amikor egy újabb adatbázis-kezelői kiadásra kell áttérnie


A következő szakasz részletesen ismerteti azokat az SAP-termékeket és adatbázis-kezelői kiadásokat, amelyeket az SAP az Azure-on Windows és Linux rendszeren is támogat. 

> [!NOTE]
> A különböző adatbázis-kezelők minimális kiadásai körültekintően vannak kiválasztva, és előfordulhat, hogy nem mindig tükrözik az adatbázis-kezelők teljes spektrumát, így az Azure-ban általában a különböző adatbázis-szolgáltatók támogatása Számos SAP-munkaterhelés kapcsolatos szempontot figyelembe vettünk a minimális kiadások meghatározásához. A régebbi adatbázis-kezelői kiadások tesztelése és minősítése nem lehetséges. 

> [!NOTE]
> A felsorolt minimális kiadások az operációs rendszerek régebbi verzióját és az adatbázis-kiadásokat jelölik. Javasoljuk, hogy a legújabb operációsrendszer-kiadásokat és adatbázis-kiadásokat használja. Sok esetben az újabb operációs rendszerek és adatbázisok kiadása a nyilvános felhőben történő futtatás használati esetét vette figyelembe és adaptált kódot a nyilvános felhőben vagy konkrétabban az Azure-ban való futtatásra való optimalizáláshoz

## <a name="oracle-dbms-support"></a>Oracle adatbázis-kezelői támogatás
Az Azure-ban támogatott operációs rendszer, Oracle adatbázis-kezelői kiadások és Oracle-funkciók külön szerepelnek az [SAP-támogatási megjegyzésekben #2039619](https://launchpad.support.sap.com/#/notes/2039619). A Megjegyzés lényege a következőképpen összegezhető:

- A NetWeaver számára hitelesített Azure-beli virtuális gépeken támogatott minimális Oracle-kiadás az Oracle 11g-kiadás 2 patchset 3 (11.2.0.4)
- A vendég operációs rendszerek csak Windows és Oracle Linux minősítéssel rendelkeznek. Az operációs rendszer és a kapcsolódó minimális adatbázis-kezelői kiadások pontos kiadásait a Megjegyzés tartalmazza
- A Oracle Linux támogatása az Oracle adatbázis-kezelői ügyfélre is kiterjed. Ez azt jelenti, hogy az összes SAP-összetevő, például a ABAP vagy a Java stack párbeszédpanel-példányainak Oracle Linux is futniuk kell. Csak olyan SAP-összetevők futhatnak, amelyek nem csatlakoznak az Oracle adatbázis-kezelő rendszerhez, hanem egy másik Linux operációs rendszert futtatnak.
- Az Oracle RAC nem támogatott 
- Az Oracle ASM bizonyos esetekben támogatott. A részletek a megjegyzésben vannak felsorolva
- A nem Unicode típusú SAP rendszerek csak a Windows vendég operációs rendszert futtató alkalmazás-kiszolgálókon támogatottak. Az adatbázis-kezelő rendszer vendég operációs rendszere lehet Oracle Linux vagy Windows. Ennek a korlátozásnak az oka az SAP-termék rendelkezésre állási mátrixának (PAM) ellenőrzésekor látható. A Oracle Linux esetében az SAP soha nem jelent meg Unicode-alapú SAP-kerneleket

A megcélzott Azure-infrastruktúrával támogatott adatbázis-kezelői kiadások ismeretében ellenőriznie kell az SAP-termékek rendelkezésre állási mátrixát, hogy támogatott-e a futtatni kívánt SAP-termék kiadása. 

## <a name="oracle-linux"></a>Oracle Linux
Oracle Linux a legjelentősebb feltett kérdés, hogy az SAP támogatja-e a Oracle Linux szerves részét képező Red Hat kernelt is. Részletekért olvassa el az [SAP-támogatási megjegyzés #1565179](https://launchpad.support.sap.com/#/notes/1565179).

## <a name="other-database-than-sap-hana"></a>Más adatbázis, mint SAP HANA
A nem HANA-adatbázisok SAP-munkaterheléshez való támogatását az [SAP-támogatási megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533)ismerteti.


## <a name="sap-hana-support"></a>SAP HANA támogatás
Az Azure-ban két szolgáltatás található, amelyek a HANA-adatbázisok futtatására használhatók:

- Azure Virtual Machines
- [HANA nagyméretű példányai](./hana-overview-architecture.md)

A SAP HANA futtatásához az SAP több és erősebb feltételekkel rendelkezik, és az infrastruktúrának meg kell felelnie, mint a NetWeaver vagy más SAP-alkalmazások és az adatbázis-kezelők futtatása Ennek eredményeképpen kisebb számú Azure-beli virtuális gép felel meg a SAP HANA adatbázis-kezelő rendszer futtatásának. A SAP HANA által támogatott Azure-infrastruktúra listája az úgynevezett [SAP HANA Hardware könyvtárban](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)található. 

> [!NOTE]
> A "betű" karakterrel kezdődő egység a [HANA Large instances](./hana-overview-architecture.md) egység. 

> [!NOTE]
> Az SAP nem rendelkezik a SAP HANA fő kiadásaitól függő speciális minősítéssel. A közös véleménysel ellentétben a [Hana Certified IaaS platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)az oszlop **minősítési forgatókönyve** nem tesz **utasítást a Hana Major vagy** az alverzió minősítésére. Azt feltételezheti, hogy a HANA 1,0 és a HANA 2,0 számára felhasználható összes egység a Hana 1,0 kiadásokban is támogatott, ha az adott egységekhez tartozó minősített operációsrendszer-kiadásokat is támogatja. 

SAP HANA használata esetén a különböző minimális operációsrendszer-kiadásokra lehet érvényes, mint az általános NetWeaver-esetekben. Az egyes egységekhez tartozó támogatott operációs rendszerek mindegyikét külön kell megtekinteni, mivel ezek eltérőek lehetnek. Ezt úgy teheti meg, hogy az egyes egységekre kattint. További részletek jelennek meg. A felsorolt részletek egyike az adott egységhez támogatott különböző operációs rendszerek.

> [!NOTE]
> Az Azure HANA nagyméretű példányai az Azure-beli virtuális gépekhez képest szigorúbban korlátozzák a támogatott operációs rendszereket. A másik Azure-beli virtuális gépeken a minimális kiadásoknak megfelelően lehet újabb operációs kiadásokat érvényesíteni. Ez különösen igaz a linuxos kernelek megváltoztatásához szükséges nagyobb virtuálisgép-egységek némelyikére

Az Azure-infrastruktúra támogatott operációs rendszerének ismeretében ellenőriznie kell a [SAP támogatási megjegyzéseit #2235581](https://launchpad.support.sap.com/#/notes/2235581) a célzott Azure-egységekben támogatott pontos SAP HANA kiadásokhoz és javítási szintekhez. 

> [!IMPORTANT]
> Nagyon fontos a pontos SAP HANA kiadások és a javítási szintek ellenőrzésének lépései. Sok esetben egy adott operációsrendszer-kiadás támogatása a SAP HANA végrehajtható fájl adott javítási szintjétől függ.

Amint ismeri a megcélzott Azure-infrastruktúrában futtatott HANA-kiadásokat, be kell jelentkeznie az SAP-termékek rendelkezésre állási mátrixában annak megállapításához, hogy vannak-e korlátozások a kiszűrt HANA-kiadásokat támogató SAP-termékek kiadásaihoz


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Hitelesített Azure-beli virtuális gépek és HANA nagyméretű példány-egységek és üzleti tranzakciók átviteli sebessége
A támogatott operációs rendszerek kiadásainak kiértékelése mellett az adatbázis-kezelői kiadások és a függő támogatás az Azure-infrastruktúra egységei számára elérhető SAP-kiadásokat is igénybe vehetjük Az összes SAP-méretezés a nedv számításaitól függ. A meglévő SAP-rendszerek kiértékelése általában az infrastruktúra-szolgáltató segítségével számítja ki az egységek NEDVét. Az adatbázis-kezelő réteghez és az alkalmazás rétegéhez. Más esetekben, amikor új funkciók jönnek létre, az SAP-vel való méretezési gyakorlat felfedi az alkalmazási réteg és az adatbázis-kezelő réteg szükséges SAP-számát. Mivel az infrastruktúra-szolgáltató a Microsoft feladata, hogy biztosítsa az SAP átviteli sebességének jellemzését a NetWeaver és/vagy HANA tanúsítvánnyal rendelkező különböző egységek számára.

Az Azure-beli virtuális gépek esetében ezek az SAP-átviteli sebességek az [SAP-támogatási megjegyzés #1928533ban](https://launchpad.support.sap.com/#/notes/1928533)vannak dokumentálva. Az Azure HANA nagyméretű példányai esetében az [SAP-támogatási megjegyzésekkel](https://launchpad.support.sap.com/#/notes/2316233) kapcsolatos dokumentációban #2316233

Az [SAP-támogatási megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533)a következő megjegyzések érvényesek:

- **Az M-sorozatú Azure-beli virtuális gépek és Mv2-Series Azure-beli virtuális gépek esetében a különböző minimális operációsrendszer-kiadások érvényesek, mint a többi Azure-beli VM** A legújabb operációsrendszer-verziókra vonatkozó követelmény azon alapul, hogy a különböző operációs rendszerek gyártóinak az operációs rendszerük által kiadott kiadásaiban az adott Azure-beli virtuálisgép-típusokon futó operációs rendszerek, illetve a virtuális gépekre vonatkozó SAP-munkaterhelések teljesítményének és teljesítményének optimalizálása
- Két tábla különböző virtuálisgép-típusokat határoz meg. A második táblázat a kizárólag az Azure standard Storage-t támogató Azure-beli virtuálisgép-típusok SAP átviteli sebességét határozza meg. Az adatbázis-kezelő üzembe helyezése a Megjegyzés második táblájában megadott egységeken nem támogatott.


## <a name="other-sap-products-supported-on-azure"></a>Az Azure-ban támogatott egyéb SAP-termékek
A feltételezés általánosságban azt feltételezi, hogy a nagy kapacitású felhők (például az Azure) esetében a legtöbb SAP-szoftver működési problémák nélkül fut az Azure-ban. A saját Felhőbeli vizualizációkkal szemben azonban az SAP továbbra is támogatja a különböző SAP-termékeket kifejezetten a különböző hyerpscale-alapú felhőalapú szolgáltatók számára. Ennek eredményeképpen különböző SAP-támogatási megjegyzések jelennek meg, amelyek az Azure különböző SAP-termékekhez való támogatását jelzik. 

A Business Objects BI platform esetében az [SAP-támogatási megjegyzés #2145537](https://launchpad.support.sap.com/#/notes/2145537) az Azure-ban támogatott SAP Business Objects-termékek listáját tartalmazza. Ha kérdése van a szoftverek olyan összetevőire vagy verzióira, amelyek úgy tűnik, hogy nem szerepelnek a listához vagy a támogatáshoz, és amelyek a felsorolt minimális kiadásoknál frissebbek, akkor egy SAP-támogatási kérelmet kell megnyitnia az általa támogatott összetevővel kapcsolatban.

Üzleti objektumok Data Services esetén az [SAP-támogatási megjegyzés #22288344](https://launchpad.support.sap.com/#/notes/2288344) ismerteti az Azure-ban futó sap-Data Services minimális támogatását. 

> [!NOTE]
> Ahogy azt az SAP-támogatási Megjegyzés jelzi, be kell jelentkeznie az SAP PAM-ban az Azure-ban támogatott megfelelő támogatási csomag szintjének azonosításához

Az SAP Datahub/Vora-támogatás az Azure Kubernetes Servicesben (ak) részletesen szerepel az [SAP-támogatási megjegyzésben #2464722](https://launchpad.support.sap.com/#/notes/2464722)

Az SAP bites 10,1 SP08 támogatását az [SAP támogatási megjegyzése #2451795](https://launchpad.support.sap.com/#/notes/2451795)

Az SAP Hybris kereskedelmi platformjának támogatása az Azure-ban részletesen szerepel a [Hybris dokumentációjában](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html). Az SAP Hybris kereskedelmi platform támogatott adatbázis-kezelője az alábbi, például:

- SQL Server és Oracle a Windows operációs rendszer platformján. Az SAP NetWeaver esetében ugyanazok a minimális kiadások érvényesek. A részletekért lásd az [SAP támogatási megjegyzéseit #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- SAP HANA Red Hat és SUSE Linux rendszeren. SAP HANA Certified VM-típusok szükségesek a [jelen dokumentumban](#sap-hana-support)korábban dokumentált módon. Az SAP (Hybris) kereskedelmi platform OLTP számítási feladatnak számít
- SQL Azure DB az SAP (Hybris) kereskedelmi platform 1811-es verziójával




## <a name="next-steps"></a>Következő lépések
Az [Azure Virtual Machines tervezésének és megvalósításának](./planning-guide.md) következő lépéseinek elolvasása az SAP NetWeaver-ban
