---
title: Azure-beli SAP HANA operatív modellje (nagyméretű példányok) | Microsoft Docs
description: SAP HANA operatív modellje az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9660540c60c6b3a734dc507a6056af9f1f8d6dc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675561"
---
# <a name="operations-model-and-responsibilities"></a>Működési modell és feladatok

Az Azure-ban (nagyméretű példányok) SAP HANA által biztosított szolgáltatás az Azure IaaS-szolgáltatásokkal van összhangban. A HANA nagyméretű példány egy példánya egy telepített operációs rendszerrel rendelkezik, amely SAP HANAre van optimalizálva. Csakúgy, mint az Azure IaaS virtuális gépek esetében, az operációs rendszer megerősítő feladatai, a további szoftverek telepítése, a HANA telepítése, az operációs rendszer és a HANA működtetése, valamint az operációs rendszer és a HANA az Ön felelőssége. A Microsoft nem kényszeríti az operációs rendszer frissítéseit és a HANA-frissítéseket.

![Az Azure-beli SAP HANA feladatai (nagyméretű példányok)](./media/hana-overview-architecture/image2-responsibilities.png)

Ahogy az ábrán látható, a SAP HANA az Azure-ban (nagyméretű példányok) egy több-bérlős IaaS ajánlat. A felelősség megosztásának a legtöbb esetben az operációs rendszer-infrastruktúra határa. A Microsoft felelős a szolgáltatás minden aspektusa számára az operációs rendszer sorában. A szolgáltatás a sorban lévő összes aspektusa felelős. Az operációs rendszer az Ön felelőssége. Továbbra is használhatja a legtöbb aktuális helyszíni módszert, amelyet a megfelelőség, a biztonság, az alkalmazások kezelése, az alap és az operációs rendszer kezelése során alkalmazhat. A rendszerek úgy jelennek meg, mintha a hálózatban vannak.

Ez a szolgáltatás SAP HANAre van optimalizálva, ezért vannak olyan területek, ahol a Microsofttal kell együttműködni a legjobb eredmények érdekében a mögöttes infrastruktúra-funkciók használatához.

Az alábbi lista részletesen ismerteti az egyes rétegeket és a felelősségi köröket:

**Hálózatkezelés**: a SAP HANAt futtató nagyméretű példányok bélyegzője összes belső hálózata. Az Ön felelőssége magában foglalja a tároláshoz való hozzáférést, a példányok közötti kapcsolatot (a kibővíthető és egyéb funkciókhoz), a környezethez való kapcsolódást és az Azure-beli kapcsolódást, ahol az SAP-alkalmazás rétege a virtuális gépeken található Az Azure-adatközpontok közötti WAN-kapcsolatot is magában foglalja a vész-helyreállítási célú replikációhoz. Az összes hálózatot a bérlő particionálja, és minőségi szolgáltatást alkalmaz.

**Storage**: az SAP HANA-kiszolgálók által igényelt összes kötet virtualizált particionált tárolója, valamint a pillanatképek. 

**Kiszolgálók**: a dedikált fizikai kiszolgálók, amelyek a bérlők számára hozzárendelt SAP HANA adatbázisok futtatására szolgálnak. Az I. típusú SKU-I osztályba tartozó kiszolgálók hardveres absztraktak. Az ilyen típusú kiszolgálók esetében a kiszolgálói konfiguráció összegyűjtése és karbantartása a profilokban történik, amelyek az egyik fizikai hardverről egy másik fizikai hardverre helyezhetők át. A profilok műveleteinek ilyen (manuális) áthelyezése egy kicsit az Azure-szolgáltatások gyógyítására is hasonlítható. A II kategóriájú SKU-i kiszolgálók nem nyújtanak ilyen képességet.

**SDDC**: az adatközpontok szoftver által definiált entitásként való kezeléséhez használt felügyeleti szoftver. Lehetővé teszi a Microsoft számára az erőforrások méretezési, rendelkezésre állási és teljesítménybeli kiosztását.

**O/S**: a kiszolgálókon futó operációs rendszer (SUSE Linux vagy Red Hat Linux). A megadott operációsrendszer-lemezképeket az egyes linuxos gyártók adták ki a Microsoftnak a SAP HANA futtatásához. Az adott SAP HANA-re optimalizált rendszerképekhez előfizetéssel kell rendelkeznie a linuxos gyártónál. Ön felelős a lemezképeknek az operációs rendszer gyártójával való regisztrálásához. 

A Microsoft által átadott időpontban Ön felelős a Linux operációs rendszer további javításával. Ez a javítás olyan további csomagokat tartalmaz, amelyek a sikeres SAP HANA telepítéséhez szükségesek, és amelyeket az adott linuxos gyártó nem tartalmazott a SAP HANA optimalizált operációsrendszer-lemezképekben. (További információ: az SAP HANA telepítési dokumentációja és SAP-megjegyzések.) 

Az operációs rendszer javításának felelőssége az operációs rendszer és a hozzá tartozó illesztőprogramok az adott kiszolgálói hardverhez képest történő meghibásodása vagy optimalizálása miatt. Az operációs rendszer biztonsági vagy funkcionális javításának felelőssége is. 

Az Ön felelőssége a következők figyelését és kapacitásának megtervezését is magában foglalja:

- CPU-erőforrások felhasználása.
- Memóriahasználat.
- A szabad területtel, a IOPS és a késéssel kapcsolatos lemezes kötetek.
- Hálózati kötet forgalma a HANA nagyméretű példánya és az SAP-alkalmazás rétege között.

A HANA nagyméretű példány mögöttes infrastruktúrája az operációs rendszer kötetének biztonsági mentésére és visszaállítására szolgáló funkciókat biztosít. A funkció használata is az Ön felelőssége.

**Middleware**: a SAP HANA példány, elsődlegesen. Az adminisztráció, a műveletek és a monitorozás az Ön felelőssége. A megadott funkciókkal tárolási pillanatképeket használhat a biztonsági mentéshez és helyreállításhoz, valamint a vész-helyreállítási célokra. Ezeket a képességeket az infrastruktúra biztosítja. A feladatai közé tartozik a magas rendelkezésre állás vagy a vész-helyreállítás megtervezése ezekkel a képességekkel, a kihasználása és a figyelés annak megállapítása, hogy a tárolási Pillanatképek végrehajtása sikeres volt

**Adatokat**: a SAP HANA által felügyelt adatokat és más adatokat, például a kötetek vagy fájlmegosztás biztonsági másolatait tartalmazó fájlokat. A feladatai közé tartozik a lemez szabad területének figyelése és a kötetek tartalmának kezelése. A lemezes kötetek és a tárolási Pillanatképek biztonsági mentéseinak sikeres végrehajtásának figyelésére is felelős. Az adatreplikáció a vész-helyreállítási helyekre való sikeres végrehajtása a Microsoft feladata.

**Alkalmazások:** Az SAP-alkalmazás példányai vagy nem SAP-alkalmazások esetében az alkalmazások alkalmazási rétege. Feladatai közé tartoznak az alkalmazások üzembe helyezése, felügyelete, működtetése és figyelése. Ön felelős a CPU-erőforrások felhasználásának, a memóriahasználat, az Azure Storage-felhasználás és a virtuális hálózatokon belüli hálózati sávszélesség-használat megtervezésének. Az Azure-ban (nagyméretű példányok) SAP HANA virtuális hálózatok erőforrás-felhasználásának megtervezéséhez is felelős.

**WAN**: a helyszíni környezetből az Azure-ba történő munkaterhelések számára létrehozott kapcsolatok. A HANA nagyméretű példánnyal rendelkező ügyfelek az Azure ExpressRoute-t használják a kapcsolódáshoz. Ez a kapcsolat nem része az Azure-beli (nagyméretű példányok) megoldás SAP HANAjának. Ön felelős ennek a hálózatnak a beállításaiért.

**Archive**: érdemes lehet archiválni az adatok másolatait a Storage-fiókokban lévő saját metódusok használatával. Az archiváláshoz a felügyelet, a megfelelőség, a költségek és a műveletek szükségesek. Ön felelős az Azure-beli archív másolatok és biztonsági másolatok létrehozásában, és megfelelő módon tárolja őket.

Tekintse [meg az Azure-beli SAP HANA SLA-t (nagyméretű példányok)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Következő lépések**
- Tekintse át [SAP HANA (nagyméretű példányok) architektúráját az Azure-](hana-architecture.md) ban
