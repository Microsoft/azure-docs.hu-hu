---
title: A StorSimple 8000 Series megoldás áttekintése | Microsoft Docs
description: Leírja a StorSimple-rétegek, az eszköz, a virtuális eszköz, a szolgáltatások és a tárolók felügyeletét, és bevezeti a StorSimple használt kulcsfontosságú kifejezéseket.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/02/2021
ms.author: timlt
ms.openlocfilehash: 3f0ec053d806208b4a3c15604714f6bdb87399e7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613221"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000 sorozat: hibrid felhőalapú tárolási megoldás

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés
Üdvözli a Microsoft Azure StorSimple, amely egy integrált tárolási megoldás, amely a helyszíni eszközök és a Microsoft Azure felhőalapú tárolók közötti tárolási feladatokat kezeli. A StorSimple egy hatékony, költséghatékony és könnyen kezelhető Tárolóhálózati megoldás, amely kiküszöböli a nagyvállalati tárterülethez és az adatvédelemhez kapcsolódó számos problémát és költséget. A jogvédett StorSimple 8000 sorozatú eszközt használja, integrálható a felhőszolgáltatásokkal, és olyan eszközkészletet kínál, amellyel egyszerűen megjeleníthető minden vállalati tárhely, többek között a felhőbeli tárhely is. (A Microsoft Azure webhelyen közzétett StorSimple-telepítési információk csak a StorSimple 8000 Series-eszközökre vonatkoznak. Ha StorSimple 5000/7000 sorozatú eszközt használ, lépjen a [StorSimple súgóra](http://onlinehelp.storsimple.com/).)

A StorSimple a [tárolási rétegek](#automatic-storage-tiering) használatával kezeli a tárolt adattárakat a különböző adathordozók között. Az aktuális munkakészletet stabil állapotú meghajtókon (SSD-k) tárolják a helyszínen. A ritkábban használt adatmennyiségeket merevlemez-meghajtókon (HDD-k) tárolják, és az archiválási adatként a felhőbe kerülnek. Emellett a StorSimple a deduplikálás és a tömörítés használatával csökkenti az adatmennyiséget. További információkért keresse fel a [deduplikálás és a tömörítés](#deduplication-and-compression)című témakört. Az StorSimple 8000 Series dokumentációjában használt egyéb kulcsfontosságú kifejezések és fogalmak definícióit a cikk végén, a [StorSimple-terminológiában](#storsimple-terminology) találja.

A Storage kezelése mellett a StorSimple adatvédelmi funkciói lehetővé teszik az igény szerinti és ütemezett biztonsági mentések létrehozását, majd a helyileg vagy a felhőben történő tárolását. A biztonsági mentések növekményes Pillanatképek formájában készülnek, ami azt jelenti, hogy gyorsan létrehozhatók és visszaállíthatók. A Felhőbeli Pillanatképek kritikus fontosságúak lehetnek a vész-helyreállítási forgatókönyvekben, mert a másodlagos tárolási rendszereket (például szalagos biztonsági mentést) cserélik le, és szükség esetén lehetővé teszik az adatközpontba vagy más helyekre való visszaállítását.

![videó ikon](./media/storsimple-overview/video_icon.png) Tekintse meg a videót a Microsoft Azure StorSimple gyors bevezetéséhez.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Miért érdemes használni a StorSimple?
Az alábbi táblázat a Microsoft Azure StorSimple által biztosított főbb előnyöket ismerteti.

| Szolgáltatás | Előny |
| --- | --- |
| Transzparens integráció |Az iSCSI protokollt használja az adattárolási létesítmények láthatatlan csatolásához. A felhőben, az adatközpontban vagy a távoli kiszolgálókon tárolt adattárolók úgy tűnik, hogy egyetlen helyen tárolódnak. |
| Csökkentett tárolási költségek |Elegendő helyi vagy Felhőbeli tárterületet foglal le a jelenlegi igények kielégítéséhez, és csak szükség esetén terjeszti ki a felhőalapú tárhelyet. A szolgáltatás tovább csökkenti a tárolási követelményeket és a költségeket azáltal, hogy megszünteti az azonos adatmennyiségek redundáns verzióit (deduplikálás) és a tömörítést. |
| Egyszerűsített tárolók kezelése |Rendszerfelügyeleti eszközöket biztosít a helyszínen, távoli kiszolgálón és a felhőben tárolt adattárolás konfigurálásához és kezeléséhez. Emellett a biztonsági mentési és visszaállítási függvények a Microsoft Management Console (MMC) beépülő modulból is kezelhetők.|
| A vész-helyreállítás és-megfelelőség javítása |A nem igényel kiterjesztett helyreállítási időt. Ehelyett visszaállítja a szükséges adatok mennyiségét, hogy a normál műveletek továbbra is minimális megszakítással működjenek. Emellett házirendeket is beállíthat a biztonsági mentési ütemtervek és az adatmegőrzés megadásához. |
| Adatmobilitás |A Microsoft Azure Cloud Services szolgáltatásba feltöltött adatok a helyreállítási és áttelepítési célból más helyekről is elérhetők. Emellett a StorSimple használatával is konfigurálhatja a Microsoft Azure rendszerű virtuális gépeken futó StorSimple felhőalapú készülékeit. A virtuális gépek ezután a virtuális eszközök segítségével érhetik el a tárolt, tesztelésre vagy helyreállításra szolgáló eszközöket. |
| Az üzletmenet folytonossága |Lehetővé teszi, hogy a StorSimple 5000-7000 sorozat felhasználói áttelepítsenek az StorSimple 8000 Series-eszközre. |
| Rendelkezésre állás a Azure Government portálon |A StorSimple a Azure Government portálon érhető el. További információ: [helyszíni StorSimple-eszköz üzembe helyezése a kormányzati portálon](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Adatvédelem és rendelkezésre állás |A StorSimple 8000 sorozat a helyileg redundáns tárolás (LRS) és a Geo-redundáns tárolás (GRS) mellett támogatja a zóna redundáns tárolását (ZRS). Tekintse [meg ezt a cikket az Azure Storage redundancia-beállításairól](../storage/common/storage-redundancy.md) a ZRS részleteinek megtekintéséhez. |
| Kritikus alkalmazások támogatása |A StorSimple lehetővé teszi, hogy a megfelelő köteteket helyileg rögzített módon azonosítsa, így biztosítva, hogy a kritikus fontosságú alkalmazások által igényelt adatmennyiség ne legyen a felhőbe bontva. A helyileg rögzített kötetek nem vonatkoznak a Felhőbeli késésekre vagy a kapcsolódási problémákra. További információ a helyileg rögzített kötetekről: [a kötetek kezelésére szolgáló StorSimple Eszközkezelő szolgáltatás használata](storsimple-8000-manage-volumes-u2.md). |
| Kis késleltetés és nagy teljesítmény |Létrehozhat olyan felhőalapú berendezéseket, amelyek kihasználják az Azure Premium Storage nagy teljesítményű, kis késleltetésű funkcióit. További információ a StorSimple prémium szintű felhőalapú készülékekről: [StorSimple Cloud Appliance üzembe helyezése és kezelése az Azure-ban](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>StorSimple-összetevők
A Microsoft Azure StorSimple megoldás a következő összetevőket tartalmazza:

* **Microsoft Azure StorSimple eszköz** – egy helyszíni hibrid tárolási tömb, amely SSD-ket és HDD-ket tartalmaz, redundáns vezérlőkkel és automatikus feladatátvételi képességekkel együtt. A vezérlők a tárolási rétegek kezelését, a jelenleg használt (vagy forró) adatoknak a helyi tárolón való elhelyezését (az eszközön vagy a helyszíni kiszolgálókon) a ritkábban használt adatok felhőbe való áthelyezésével kezelik.
* **StorSimple Cloud Appliance** – más néven a StorSimple virtuális készülék. A StorSimple-eszköz szoftveres verziója, amely a fizikai hibrid tárolóeszköz architektúráját és képességeit replikálja. A StorSimple Cloud Appliance egy Azure-beli virtuális gép egyetlen csomópontján fut. Az Azure Premium Storage előnyeit kihasználó prémium szintű virtuális eszközök a 2. frissítésben és az újabb verziókban is elérhetők.
* **StorSimple Eszközkezelő Service** – a Azure Portal egy bővítménye, amely lehetővé teszi egy StorSimple-eszköz vagy-StorSimple Cloud Appliance kezelését egyetlen webes felületen. A StorSimple Eszközkezelő szolgáltatással szolgáltatásokat hozhat létre és kezelhet, eszközöket tekinthet meg és kezelhet, megtekintheti a riasztásokat, kezelheti a köteteket, valamint megtekintheti és kezelheti a biztonsági mentési házirendeket és a biztonsági mentési katalógust.
* **Windows PowerShell StorSimple-bővítménye** – parancssori felület, amely a StorSimple-eszköz felügyeletére használható. Windows PowerShell StorSimple-bővítménye rendelkezik olyan funkciókkal, amelyek lehetővé teszik a StorSimple-eszköz regisztrálását, a hálózati adapter konfigurálását az eszközön, bizonyos típusú frissítések telepítését, az eszköz hibakeresését a támogatási munkamenet elérésével, és az eszköz állapotának módosítását. Windows PowerShell StorSimple-bővítménye a soros konzolhoz való csatlakozással vagy a Windows PowerShell-távelérés használatával érhető el.
* **Azure PowerShell StorSimple-parancsmagok** – olyan Windows PowerShell-parancsmagok gyűjteménye, amelyek segítségével automatizálhatja a szolgáltatás-és áttelepítési feladatokat a parancssorból. A StorSimple Azure PowerShell-parancsmagokkal kapcsolatos további információkért nyissa meg a [parancsmag-referenciát](/powershell/module/servicemanagement/azure.service/#azure).
* **StorSimple Snapshot Manager** – egy MMC beépülő modul, amely mennyiségi csoportokat és a Windows Kötet árnyékmásolata szolgáltatás használ az alkalmazással konzisztens biztonsági másolatok létrehozásához. Emellett a StorSimple Snapshot Manager használatával biztonsági mentési ütemterveket hozhat létre, illetve klónozott vagy helyreállíthatja a köteteket.
* **StorSimple-adapter a sharepointhoz** – ez egy olyan eszköz, amely transzparens módon kiterjeszti Microsoft Azure StorSimple tárolót és az adatvédelmet a SharePoint Server-farmokra, miközben a StorSimple-tárolót megtekintheti és kezelheti a SharePoint központi felügyeleti portálján.

Az alábbi ábrán a Microsoft Azure StorSimple architektúrájának és összetevőinek áttekintése látható.

![StorSimple architektúra](./media/storsimple-overview/overview-big-picture.png)

Az alábbi szakaszok részletesebben ismertetik ezeket az összetevőket, és elmagyarázzák, hogyan rendezi a megoldás az adatokat, lefoglalja a tárterületet, és megkönnyíti a tárolási felügyeletet és az adatvédelmet. Az utolsó szakasz a StorSimple-összetevőkkel és azok kezelésével kapcsolatos fontos kifejezések és fogalmak definícióit tartalmazza.

## <a name="storsimple-device"></a>StorSimple-eszköz
A Microsoft Azure StorSimple eszköz egy helyszíni hibrid tárolási tömb, amely elsődleges tárterületet és iSCSI-hozzáférést biztosít a rajta tárolt adathoz. Kezeli a Felhőbeli tárolással folytatott kommunikációt, és segít a Microsoft Azure StorSimple-megoldáson tárolt összes adat biztonságának és titkosságának biztosításában.

A StorSimple-eszköz SSD-ket és merevlemez-meghajtókat (HDD-ket) tartalmaz, valamint támogatja a fürtözést és az automatikus feladatátvételt. Egy megosztott processzort, megosztott tárolót és két tükrözött vezérlőt tartalmaz. Mindegyik vezérlő a következőket biztosítja:

* Kapcsolódás egy gazdagép számítógéphez
* Legfeljebb hat hálózati port a helyi hálózathoz (LAN) való csatlakozáshoz
* Hardver figyelése
* Nem felejtő véletlen hozzáférésű memória (NVRAM), amely akkor is megőrzi az adatokat, ha az áramellátás megszakad
* Fürtöket támogató frissítés a feladatátvevő fürtben lévő kiszolgálókon lévő szoftverfrissítések kezeléséhez, hogy a frissítések minimális vagy semmilyen hatással legyenek a szolgáltatás rendelkezésre állására
* Fürtszolgáltatás, amely a háttér-fürthöz hasonlóan működik, magas rendelkezésre állást biztosít, és minimalizálja az esetleges, ha egy HDD vagy SSD meghibásodik vagy offline állapotba kerül.

Egy adott időpontban csak egy vezérlő aktív. Ha az aktív vezérlő meghibásodik, a második vezérlő automatikusan aktívvá válik.

További információkért keresse fel a [StorSimple hardver összetevői és állapota](storsimple-8000-monitor-hardware-status.md)című témakört.

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
A StorSimple használatával olyan felhőalapú berendezést hozhat létre, amely replikálja a fizikai hibrid tárolóeszköz architektúráját és képességeit. A StorSimple Cloud Appliance (más néven a StorSimple virtuális berendezés) egy Azure-beli virtuális gép egyetlen csomópontján fut. (A felhőalapú berendezés csak Azure-beli virtuális gépen hozható létre. Nem hozhat létre egyet egy StorSimple-eszközön vagy egy helyszíni kiszolgálón.)

A felhőalapú berendezés a következő funkciókat tartalmazza:

* Úgy viselkedik, mint egy fizikai berendezés, és iSCSI-felületet tud biztosítani a felhőben lévő virtuális gépekhez.
* Korlátlan számú felhőalapú készüléket hozhat létre a felhőben, és szükség szerint be-és kikapcsolhatja őket.
* Segítheti a helyszíni környezetek szimulálását a vész-helyreállítási, fejlesztési és tesztelési forgatókönyvekben, és segítséget nyújthat a biztonsági másolatok elemszintű lekéréséhez.

A StorSimple Cloud Appliance két modellben érhető el: a 8010-es eszközön (korábbi nevén 1100 modell) és a 8020 eszközön. Az 8010-es eszköz kapacitása legfeljebb 30 TB. Az Azure Premium Storage előnyeit kihasználó 8020-es eszköz maximális kapacitása 64 TB. (A helyi szinteken az Azure Premium Storage tárolja az SSD-ket, míg a standard Storage a HDD-on tárolja az adattárakat.) A Premium Storage használatához rendelkeznie kell egy prémium szintű Azure Storage-fiókkal.

További információ a StorSimple Cloud Applianceről [: StorSimple Cloud Appliance üzembe helyezése és kezelése az Azure-ban](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>StorSimple-eszközkezelő szolgáltatás
A Microsoft Azure StorSimple webalapú felhasználói felületet (StorSimple Eszközkezelő szolgáltatást) biztosít, amely lehetővé teszi az adatközpontok és a felhőalapú tárolók központi kezelését. A következő feladatok elvégzéséhez használhatja a StorSimple Eszközkezelő szolgáltatást:

* Konfigurálja a rendszerbeállításokat a StorSimple-eszközökhöz.
* StorSimple-eszközök biztonsági beállításainak konfigurálása és kezelése.
* Felhőbeli hitelesítő adatok és tulajdonságok konfigurálása.
* Kötetek konfigurálása és kezelése egy kiszolgálón.
* Állítsa be a kötetek csoportjait.
* Az adatbiztonsági mentés és a visszaállítás.
* A teljesítmény figyelése.
* Tekintse át a rendszerbeállításokat, és azonosítsa a lehetséges problémákat.

A StorSimple Eszközkezelő szolgáltatással az összes felügyeleti feladatot elvégezheti, kivéve a rendszer leállását igénylő feladatokat, például a kezdeti telepítést és a frissítések telepítését.

További információért látogasson el [a StorSimple Eszközkezelő szolgáltatás használatára a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell StorSimple-bővítménye
A Windows PowerShell StorSimple-bővítménye parancssori felületet biztosít a Microsoft Azure StorSimple szolgáltatás létrehozásához és kezeléséhez, valamint a StorSimple-eszközök beállításához és figyeléséhez. Ez egy Windows PowerShell-alapú parancssori felület, amely dedikált parancsmagokat tartalmaz a StorSimple-eszköz kezeléséhez. A Windows PowerShell StorSimple-bővítménye szolgáltatásai a következőket teszik lehetővé:

* Eszköz regisztrálása.
* Konfigurálja a hálózati adaptert egy eszközön.
* Bizonyos típusú frissítések telepítése.
* A támogatási munkamenethez való hozzáféréssel elháríthatja az eszközt.
* Módosítsa az eszköz állapotát.

A Windows PowerShell StorSimple-bővítménye egy soros konzolról (közvetlenül az eszközhöz csatlakozó gazdagépen) vagy távolról a Windows PowerShell távelérés használatával érhető el. Néhány Windows PowerShell StorSimple-bővítménye feladat, például a kezdeti eszköz regisztrálása csak a soros konzolon hajtható végre.

További információért látogasson el a [Windows PowerShell StorSimple-bővítménye használatára az eszköz felügyeletéhez](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple-parancsmagok
A Azure PowerShell StorSimple-parancsmagok olyan Windows PowerShell-parancsmagok gyűjteményei, amelyek lehetővé teszik a parancssorból a szolgáltatás-és áttelepítési feladatok automatizálását. A StorSimple Azure PowerShell-parancsmagokkal kapcsolatos további információkért nyissa meg a [parancsmag-referenciát](/powershell/module/servicemanagement/azure.service/).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modul, amely a helyi és a Felhőbeli adatbiztonsági másolatok konzisztens, időponthoz kötött biztonsági másolatának létrehozásához használható. A beépülő modul egy Windows Server-alapú gazdagépen fut. A StorSimple Snapshot Manager a következőre használhatja:

* Kötetek konfigurálása, biztonsági mentése és törlése.
* Konfigurálja a mennyiségi csoportokat annak biztosítására, hogy a biztonsági másolatok az alkalmazások konzisztensek legyenek.
* Biztonsági mentési házirendek kezelése, hogy az adatbiztonsági mentés előre meghatározott ütemterv alapján történjen, és egy kijelölt helyen (helyileg vagy a felhőben) legyen tárolva.
* Kötetek és egyedi fájlok visszaállítása.

A biztonsági mentések pillanatképként vannak rögzítve, amely csak a legutóbbi pillanatkép óta történt változásokat rögzíti, és sokkal kevesebb tárterületet igényel, mint a teljes biztonsági mentés. Biztonsági mentési ütemezéseket hozhat létre, vagy igény szerint azonnali biztonsági mentést készíthet. Emellett a StorSimple Snapshot Manager használatával olyan adatmegőrzési házirendeket hozhat létre, amelyek azt szabályozzák, hogy hány pillanatképet fog menteni a rendszer. Ha később vissza kell állítania az adatok biztonsági mentésből való visszaállítását, a StorSimple Snapshot Manager lehetővé teszi a helyi vagy Felhőbeli Pillanatképek katalógusból való kiválasztását. 

Ha katasztrófa következik be, vagy ha egy másik ok miatt vissza kell állítania az adatgyűjtést, a StorSimple Snapshot Manager növekményes módon visszaállítja, ahogy szükséges. Az adatok visszaállítása nem igényli a teljes rendszer leállítását egy fájl visszaállításakor, a berendezések cseréjével vagy a műveletek másik helyre való áthelyezésével.

További információért látogasson el a [Mi az StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>SharePointhoz készült StorSimple-adapter
Microsoft Azure StorSimple tartalmazza a SharePointhoz készült StorSimple-adaptert, amely egy opcionális összetevő, amely transzparens módon kiterjeszti a StorSimple tároló-és adatvédelmi funkcióit a SharePoint Server-farmokra. Az adapter egy távoli Blob Storage (RBS) szolgáltatóval és az SQL Server RBS szolgáltatással működik, amely lehetővé teszi a Blobok áthelyezését egy, a Microsoft Azure StorSimple rendszer által készített kiszolgálóra. Microsoft Azure StorSimple a BLOB-adatokat helyileg vagy a felhőben tárolja a használat alapján.

A SharePoint rendszerhez készült StorSimple-adapter a SharePoint központi felügyeleti portálján található. A SharePoint-felügyelet továbbra is központosított marad, és úgy tűnik, hogy a SharePoint-farmban minden tárterület megjelenik.

További információkért keresse fel a [StorSimple-adapter a sharepointhoz](storsimple-adapter-for-sharepoint.md)című témakört. 

## <a name="storage-management-technologies"></a>Storage-felügyeleti technológiák
A dedikált StorSimple-eszközön, a virtuális eszközön és az egyéb összetevőkön kívül a Microsoft Azure StorSimple a következő szoftver-technológiákat használja az adatok gyors eléréséhez és a tárolási felhasználás csökkentéséhez:

* [Automatikus tárolási rétegek](#automatic-storage-tiering) 
* [Dinamikus kiosztás](#thin-provisioning) 
* [Deduplikálás és tömörítés](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatikus tárolási rétegek
A Microsoft Azure StorSimple az adatokat az aktuális használat, az életkor és a más adatokkal való kapcsolat alapján automatikusan rendezi a logikai rétegekbe. A legtöbb aktív adattárolás helyileg történik, míg a rendszer a kevésbé aktív és inaktív adatmennyiségeket automatikusan áttelepíti a felhőbe. A következő ábra szemlélteti ezt a tárolási módszert.

![StorSimple tárolási szintjei](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

A gyors hozzáférés engedélyezéséhez a StorSimple a StorSimple-eszközön lévő SSD-lemezeken nagyon aktív adatok (forró adatok) tárolására. A szolgáltatás esetenként (meleg) tárolt, az eszközön vagy az adatközpontban található kiszolgálókon tárolt HDD-ket tárolja. Áthelyezi az inaktív és a biztonsági mentési, illetve a felhőbe archiválásra vagy megfelelőségre fenntartott adatait. 

> [!NOTE]
> A 2-es vagy újabb verzióban megadhatja a kötetet helyileg rögzítettként, ebben az esetben az adatforgalom a helyi eszközön marad, és nem a felhőbe van bontva. 


A StorSimple módosítja és átrendezi az adatokat és a tárolási hozzárendeléseket a használati minták változásakor. Előfordulhat például, hogy egyes információk kevésbé lesznek aktívak az idő múlásával. Mivel egyre kevésbé aktív, a rendszer az SSD-ről a HDD-re, majd a felhőbe telepíti át. Ha ugyanezen az adategység ismét aktívvá válik, a rendszer visszatelepíti a tárolóeszközre.

A tárolási rétegek feldolgozási folyamata a következőképpen történik:

1. A rendszergazda egy Microsoft Azure felhőalapú Storage-fiókot állít be.
2. A rendszergazda a soros konzolt és a StorSimple Eszközkezelő szolgáltatást használja (amely a Azure Portalban fut) az eszköz-és fájlkiszolgáló konfigurálásához, kötetek és adatvédelmi házirendek létrehozásához. A helyszíni gépek (például fájlkiszolgálók) az Internet kis számítógép rendszerfelületét (iSCSI) használják a StorSimple-eszköz eléréséhez.
3. Kezdetben a StorSimple az eszköz gyors SSD-szintjéről tárolja az adattárolást.
4. Ahogy az SSD-szint megközelíti a kapacitást, a StorSimple deduplikálja és tömöríti a legrégebbi adatblokkokat, és áthelyezi őket a HDD-szintre.
5. Ahogy a HDD-csomag képes a kapacitásra, a StorSimple titkosítja a legrégebbi adatblokkokat, és biztonságosan küldi el azokat a HTTPS-en keresztül a Microsoft Azure Storage-fióknak.
6. Microsoft Azure több replikát hoz létre az adatközpontban és egy távoli adatközpontban, így biztosítható, hogy az adatsérülés bekövetkezése esetén helyreállítható legyen.
7. Ha a fájlkiszolgáló a felhőben tárolt adatokra kéri a StorSimple, a rendszer zökkenőmentesen visszaadja, és a StorSimple-eszköz SSD-szintjére másol egy másolatot.

> [!IMPORTANT]
> A StorSimple használatakor ne konvertálja a blobokat archiválásra, még akkor is, ha az eszköz fázisa folyamatban van. Az adatok az eszközről való lekéréséhez a blobokat a gyakori vagy a ritka elérésű típusra kell kihasználnia, amely jelentős költségeket eredményez.

#### <a name="how-storsimple-manages-cloud-data"></a>Hogyan kezeli a StorSimple a Felhőbeli adatszolgáltatásokat

A StorSimple a pillanatképek és az elsődleges adategységek (gazdagépek által írt) alapján deduplikálja az ügyféladatokat. Míg a deduplikálás nagy mennyiségű tárolási hatékonyságot jelent, a "mi a felhőben" bonyolult kérdés. A rétegű elsődleges adatok és a pillanatképek adatátfedésben vannak egymással. A felhőben egyetlen adatrészletet is felhasználhat a rétegbeli elsődleges adatként, és több pillanatkép is hivatkozhat rájuk. Minden Felhőbeli pillanatkép biztosítja, hogy az adott időponthoz tartozó adatok másolata zárolva legyen a felhőbe, amíg a pillanatkép nem törlődik.

Az adatok csak akkor törlődnek a felhőből, ha nincsenek erre az adatokra mutató hivatkozások. Ha például egy Felhőbeli pillanatképet készítettünk a StorSimple-eszközön lévő összes adattal kapcsolatban, majd törölünk néhány elsődleges adatvesztést, akkor a rendszer azonnal megtekinti az _elsődleges_ adatvesztést. A többszintes adatokat és a biztonsági másolatokat tartalmazó _Felhőbeli adatok_ változatlanok maradnak, mivel egy pillanatkép továbbra is a Felhőbeli adatokra hivatkozik. A Felhőbeli pillanatkép törlése (és az ugyanazon adatokra hivatkozó egyéb Pillanatképek) után a Felhőbeli felhasználás csökken. A Felhőbeli adatmennyiség eltávolítása előtt győződjön meg arról, hogy a pillanatképek nem hivatkoznak az adott adatmennyiségre. Ezt a folyamatot _szemét-gyűjteménynek_ nevezzük, és az eszközön futó háttér-szolgáltatás. A Felhőbeli adatok eltávolítása nem azonnal történik, mert a szemetet gyűjtő szolgáltatás a törlés előtt ellenőrzi az adatokra vonatkozó egyéb hivatkozásokat. A szemét gyűjtésének sebessége a pillanatképek teljes számától és a teljes adatoktól függ. A Felhőbeli adatmennyiséget általában kevesebb mint egy hete takarítják meg.


### <a name="thin-provisioning"></a>Dinamikus kiosztás
A dinamikus kiépítés olyan virtualizációs technológia, amelyben a rendelkezésre álló tár úgy tűnik, hogy meghaladja a fizikai erőforrásokat. Ahelyett, hogy a megfelelő tárterületet nem kívánja megtartani, a StorSimple dinamikus kiosztást használ, hogy elegendő helyet foglaljon le az aktuális követelmények kielégítése érdekében. A felhőalapú tárolás rugalmas jellege megkönnyíti ezt a megközelítést, mivel a StorSimple növelheti vagy csökkentheti a Felhőbeli tárolást a változó igények kielégítése érdekében.

> [!NOTE]
> A helyileg rögzített kötetek nincsenek kiépítve. A csak helyi kötethez lefoglalt tárterület teljes egészében a kötet létrehozásakor lesz kiépítve.


### <a name="deduplication-and-compression"></a>Deduplikálás és tömörítés
Microsoft Azure StorSimple a tárolási követelmények további csökkentése érdekében a deduplikálás és az adattömörítést használja.

A deduplikálás csökkenti a tárolt adatkészletben lévő redundancia megszüntetésével a tárolt adatmennyiséget. Ahogy az információ megváltozik, a StorSimple figyelmen kívül hagyja a változatlan adatokat, és csak a módosításokat rögzíti. Emellett a StorSimple a szükségtelen információk azonosításával és eltávolításával csökkenti a tárolt adatok mennyiségét. 

> [!NOTE]
> A helyileg rögzített köteteken lévő adatmennyiség nem deduplikált vagy tömörítve van. A helyileg rögzített kötetek biztonsági másolatait azonban deduplikálja és tömöríti.


## <a name="storsimple-workload-summary"></a>StorSimple munkaterhelés összegzése
Az alábbi táblázat a támogatott StorSimple-számítási feladatok összefoglalóját mutatja be.

| Eset | Számítási feladat | Támogatott | Korlátozások | Verzió |
| --- | --- | --- | --- | --- |
| Együttműködés |Fájlmegosztás |Yes | |Az összes verzió |
| Együttműködés |Elosztott fájlmegosztás |Yes | |Az összes verzió |
| Együttműködés |SharePoint |Igen* |Csak helyileg rögzített kötetekkel támogatott |2. frissítés és újabb |
| Archiválási |Egyszerű fájl archiválása |Yes | |Az összes verzió |
| Virtualizáció |Virtual machines (Virtuális gépek) |Igen* |Csak helyileg rögzített kötetekkel támogatott |2. frissítés és újabb |
| Adatbázis |SQL |Igen* |Csak helyileg rögzített kötetekkel támogatott |2. frissítés és újabb |
| Videó-megfigyelés |Videó-megfigyelés |Igen* |Csak akkor támogatott, ha a StorSimple-eszköz csak erre a számítási feladatra van dedikált |2. frissítés és újabb |
| Backup |Elsődleges cél biztonsági mentése |Igen* |Csak akkor támogatott, ha a StorSimple-eszköz csak erre a számítási feladatra van dedikált |3. frissítés és újabb |
| Backup |Másodlagos cél biztonsági mentése |Igen* |Csak akkor támogatott, ha a StorSimple-eszköz csak erre a számítási feladatra van dedikált |3. frissítés és újabb |

*Igen&#42; – a megoldásra vonatkozó irányelvek és korlátozások érvényesek.*

A StorSimple 8000 sorozatú eszközök nem támogatják a következő munkaterheléseket. Ha a StorSimple-on van telepítve, akkor ezek a számítási feladatok nem támogatott konfigurációt eredményeznek.

* Orvosi képalkotás
* Exchange
* Virtuális asztali infrastruktúra (VDI)
* Oracle
* SAP
* Big data
* Tartalom terjesztése
* Rendszerindítás SCSI-ről

A következő lista a StorSimple által támogatott infrastruktúra-összetevőket tartalmazza.

| Eset | Számítási feladat | Támogatott | Korlátozások | Verzió |
| --- | --- | --- | --- | --- |
| Általános kérdések |Express Route |Yes | |Az összes verzió |
| Általános kérdések |DataCore FC |Igen* |Támogatott a DataCore SANsymphony |Az összes verzió |
| Általános kérdések |DFSR |Igen* |Csak helyileg rögzített kötetekkel támogatott |Az összes verzió |
| Általános kérdések |Indexelés |Igen* |A többszintes kötetek esetében csak a metaadatok indexelése támogatott (nincs adat).<br>A helyileg rögzített kötetek esetében a teljes indexelés támogatott. |Az összes verzió |
| Általános kérdések |Víruskereső |Igen* |A többszintes kötetek esetében csak a Megnyitás és bezárás vizsgálat támogatott.<br> A helyileg rögzített kötetek esetében a teljes vizsgálat támogatott. |Az összes verzió |

*Igen&#42; – a megoldásra vonatkozó irányelvek és korlátozások érvényesek.*

A következő lista a StorSimple által a megoldások létrehozásához használt egyéb szoftvereket tartalmazza.

| Munkaterhelés típusa | StorSimple használt szoftverek | Támogatott verziók|Hivatkozás a megoldási útmutatóra| 
| --- | --- | --- | --- |
| Biztonsági mentés célja |Veeam |Veeam v 9-es és újabb verziók |[StorSimple biztonsági mentési célként a Veaam](storsimple-configure-backup-target-veeam.md)|
| Biztonsági mentés célja |A Veritas Backup Exec |Backup Exec 16 és újabb verziók |[StorSimple biztonsági mentési célként a Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Biztonsági mentés célja |VERITAS NetBackup |NetBackup 7.7. x és újabb verziók  |[StorSimple biztonsági mentési célként a NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Globális fájlmegosztás <br></br> Együttműködés |Talon  |[StorSimple Talonral](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>StorSimple-terminológia
A Microsoft Azure StorSimple-megoldás üzembe helyezése előtt javasoljuk, hogy tekintse át a következő feltételeket és definíciókat.

### <a name="key-terms-and-definitions"></a>A legfontosabb feltételek és definíciók
| Kifejezés (betűszó vagy rövidítés) | Description |
| --- | --- |
| hozzáférés-vezérlési rekord (ACR) |A Microsoft Azure StorSimple eszközön lévő kötethez társított rekord, amely meghatározza, hogy mely gazdagépek csatlakozhatnak hozzá. A meghatározás a StorSimple-eszközhöz csatlakozó gazdagépek (ACR-ben található) iSCSI minősített nevén (IQN) alapul. |
| AES-256 |256 bites Advanced Encryption Standard (AES) algoritmus a felhőbe irányuló és onnan áthelyezett adatok titkosításához. |
| foglalási egység mérete (AUS) |A Windows-fájlrendszerek fájljának tárolására lefoglalható lemezterület legkisebb mérete. Ha a fájl mérete nem a fürt méretének még többszöröse, akkor a fájl (a fürt következő többszöröse számára) felesleges helyet kell használnia, ami az elveszett helyet és a merevlemez töredezettségét eredményezi. <br>Az ajánlott Azure StorSimple-kötetek 64 KB méretűek, mert jól működik a deduplikáló algoritmusokkal. |
| automatizált tárolási rétegek |A kevésbé aktív adatok automatikus áthelyezése az SSD-ről a HDD-re, majd a felhőben lévő egyik szintjére, majd a központi felhasználói felületen lévő összes tárterület kezelésének engedélyezése. |
| biztonsági mentési katalógus |Biztonsági másolatok gyűjteménye, amely általában a használt alkalmazás típusától van összefüggésben. Ez a gyűjtemény a StorSimple Eszközkezelő szolgáltatás felhasználói felületének biztonsági mentési katalógus paneljén jelenik meg. |
| biztonságimásolat-katalógus fájlja |A StorSimple Snapshot Manager biztonsági mentési adatbázisában jelenleg tárolt elérhető Pillanatképek listáját tartalmazó fájl. |
| biztonsági mentési szabályzat |A kötetek, a biztonsági mentés típusa, valamint egy ütemezés, amely lehetővé teszi, hogy a biztonsági mentéseket előre meghatározott ütemezés szerint hozza létre. |
| bináris nagyméretű objektumok (Blobok) |Az adatbázis-kezelő rendszerekben egyetlen entitásként tárolt bináris adathalmaz. A Blobok jellemzően képek, hang vagy más multimédiás objektumok, bár a bináris végrehajtható kódokat a rendszer BLOBként tárolja. |
| Challenge Handshake Authentication Protocol (CHAP) |A kapcsolat társának hitelesítésére szolgáló protokoll a jelszóval vagy titkos kulccsal megosztó társ alapján. A CHAP lehet egyirányú vagy kölcsönös. Egyirányú CHAP esetén a cél hitelesíti a kezdeményezőt. A kölcsönös CHAP megköveteli, hogy a cél hitelesítse a kezdeményezőt, és hogy a kezdeményező hitelesítse a célt. |
| clone (klónozás) |Egy kötet duplikált példánya. |
| Felhő (CaaT) |A Felhőbeli tárolás a tárolási architektúrában rétegként integrált, így úgy tűnik, hogy minden tárterület egy vállalati tárolóeszközhöz tartozik. |
| felhőalapú szolgáltató (CSP) |A felhőalapú számítástechnikai szolgáltatások szolgáltatója. |
| Felhőbeli pillanatkép |A felhőben tárolt mennyiségi adatforgalom időpontra vonatkozó másolata. A Felhőbeli Pillanatképek egy másik, nem helyszíni tárolási rendszeren replikált pillanatképnek felelnek meg. A Felhőbeli Pillanatképek különösen hasznosak a vész-helyreállítási helyzetekben. |
| Felhőbeli tárolás titkosítási kulcsa |A StorSimple-eszköz által az eszköz által a felhőbe továbbított titkosított adatforgalom eléréséhez használt jelszó vagy kulcs. |
| fürtöket támogató frissítés |Szoftverfrissítések kezelése feladatátvevő fürt kiszolgálóin, hogy a frissítések minimális vagy semmilyen hatással legyenek a szolgáltatás rendelkezésre állására. |
| datapath |Olyan funkcionális egységek gyűjteménye, amelyek egymással összekapcsolt adatfeldolgozási műveleteket hajtanak végre. |
| inaktiválás |Állandó művelet, amely megszakítja a kapcsolatot a StorSimple-eszköz és a hozzá tartozó felhőalapú szolgáltatás között. Az eszköz Felhőbeli pillanatképei a folyamat után is megmaradnak, és klónozott vagy vész-helyreállítási felhasználható. |
| lemez tükrözése |A folyamatos rendelkezésre állás biztosítása érdekében valós időben replikálja a logikai lemez köteteit külön merevlemezeken. |
| dinamikus lemez tükrözése |Logikai lemez köteteinek replikálása dinamikus lemezeken. |
| dinamikus lemezek |Egy lemezes kötet formátuma, amely a logikai lemezkezelő (LDM) használatával tárolja és kezeli az adatmennyiséget több fizikai lemez között. A dinamikus lemezek kinagyítható, így több szabad terület is elérhető. |
| Kiterjesztett lemez (EBOD) – ház |A Microsoft Azure StorSimple eszköz másodlagos bekerítése, amely további merevlemez-lemezeket tartalmaz a további tároláshoz. |
| FAT-kiépítés |Egy hagyományos tárolási kiépítés, amelyben a tárolóhelyek a várt igények alapján vannak lefoglalva (és általában az aktuális szükségleten felül). Lásd még: dinamikus *kiépítés*. |
| merevlemez-meghajtó (HDD) |Egy meghajtó, amely a forgó lemezeket használja az adattároláshoz. |
| hibrid felhőalapú tárolás |Helyi és telephelyen kívüli erőforrásokat használó tárolási architektúra, beleértve a felhőalapú tárolást. |
| Internetes kis számítógép rendszerfelülete (iSCSI) |Az adattárolók vagy létesítmények összekapcsolására szolgáló Internet Protocol (IP)-alapú tárolási hálózatkezelési szabvány. |
| iSCSI-kezdeményező |Olyan szoftver-összetevő, amely lehetővé teszi, hogy a Windows rendszerű gazdaszámítógép egy külső iSCSI-alapú tárolóeszközhöz kapcsolódjon. |
| iSCSI minősített név (IQN) |Egy iSCSI-tárolót vagy kezdeményezőt azonosító egyedi név. |
| iSCSI-tároló |Egy szoftver-összetevő, amely központosított iSCSI-lemezes alrendszereket biztosít a tárolóhelyek hálózatában. |
| élő archiválás |A tárolási módszer, amelyben az archivált adatok bármikor elérhetők (például nem a szalagon van tárolva). A Microsoft Azure StorSimple élő archiválást használ. |
| helyileg rögzített kötet |az eszközön található kötet, amely soha nem a felhőbe van bontva. |
| helyi pillanatkép |A Microsoft Azure StorSimple eszközön tárolt mennyiségi adatmennyiség időponthoz tartozó másolata. |
| Microsoft Azure StorSimple |Egy olyan hatékony megoldás, amely egy adatközpont-tárolóeszközből és-szoftverből áll, amely lehetővé teszi az informatikai szervezetek számára, hogy az adatközpont-tárolást használják. A StorSimple leegyszerűsíti az adatvédelmet és az adatkezelést, miközben csökkenti a költségeket. A megoldás a felhővel való zökkenőmentes integráció révén összevonja az elsődleges tárhelyet, az archiválást, a biztonsági mentést és a vész-helyreállítást (DR). A SAN Storage és a Felhőbeli adatkezelés nagyvállalati szintű platformon történő kombinálásával a StorSimple-eszközök lehetővé teszik az összes tárterülettel kapcsolatos igény sebességét, egyszerűségét és megbízhatóságát. |
| Energiagazdálkodási és hűtési modul (PCM) |A StorSimple-eszköz hardveres összetevői, amelyek a tápegységből és a hűtési ventilátorból állnak. Ezért a name Power és a hűtés modul. Az eszköz elsődleges bekerítésének két 764W van PCMs, míg a EBOD-ház két 580W PCMs rendelkezik. |
| elsődleges ház |Az Application Platform-vezérlőket tartalmazó StorSimple-eszköz fő bekerítése. |
| helyreállítási időre vonatkozó célkitűzés (RTO) |Az a maximális időtartam, amelyet az üzleti folyamat vagy a rendszer a katasztrófa utáni teljes helyreállítás előtt ki kell állítani. |
| sorosan csatlakoztatott SCSI (SAS) |Merevlemez-meghajtó (HDD) típusa. |
| szolgáltatás adattitkosítási kulcsa |Minden olyan új StorSimple-eszköz számára elérhető kulcs, amely regisztrálva van a StorSimple Eszközkezelő szolgáltatásban. A StorSimple Eszközkezelő szolgáltatás és az eszköz között továbbított konfigurációs adatforgalom nyilvános kulccsal van titkosítva, és ezt követően csak titkos kulccsal lehet visszafejteni az eszközön. A szolgáltatásban tárolt adattitkosítási kulcs lehetővé teszi, hogy a szolgáltatás megszerezze ezt a titkos kulcsot a visszafejtéshez. |
| szolgáltatás regisztrációs kulcsa |Egy kulcs, amely segít regisztrálni a StorSimple eszközt a StorSimple Eszközkezelő szolgáltatással, hogy a további felügyeleti műveletekhez a Azure Portal megjelenjen. |
| Kis számítógéprendszer felülete (SCSI) |A számítógépek fizikai csatlakoztatására és az azok közötti adattovábbításra vonatkozó szabványok összessége. |
| stabil állapotú meghajtó (SSD) |Olyan lemez, amely nem tartalmaz mozgó részeket; például egy flash meghajtó. |
| tárfiók |Egy adott felhőalapú szolgáltatóhoz tartozó Storage-fiókhoz társított hozzáférési hitelesítő adatok halmaza. |
| SharePointhoz készült StorSimple-adapter |Microsoft Azure StorSimple-összetevő, amely transzparens módon kiterjeszti a StorSimple-tárolót és az adatvédelmet a SharePoint Server-farmokra. |
| StorSimple-eszközkezelő szolgáltatás |A Azure Portal kiterjesztése, amely lehetővé teszi az Azure-StorSimple helyszíni és virtuális eszközökön való kezelését. |
| StorSimple Snapshot Manager |Egy Microsoft Management Console (MMC) beépülő modul a Microsoft Azure StorSimple biztonsági mentési és visszaállítási műveleteinek kezeléséhez. |
| biztonsági mentés készítése |Egy szolgáltatás, amely lehetővé teszi a felhasználó számára, hogy interaktív biztonsági másolatot készítsen egy kötetről. A kötet manuális biztonsági mentésének másik módja, ha a biztonsági mentést egy meghatározott házirend alapján veszi figyelembe. |
| dinamikus kiépítés |Az a módszer, amellyel optimalizálható a rendelkezésre álló tárolóhelyek tárolási rendszerekben való használatának hatékonysága. A dinamikus kiosztásban a tárterület több felhasználó között van lefoglalva, az egyes felhasználók által adott időpontban minimálisan szükséges lemezterület alapján. Lásd még: *FAT-kiépítés*. |
| rétegezés |Az adatok logikai csoportosításban való rendezése az aktuális használat, az életkor és más adatokkal való kapcsolat alapján. A StorSimple automatikusan rendezi az adatszinteket. |
| kötet |Meghajtók formájában bemutatott logikai tárolóhelyek. A StorSimple-kötetek megfelelnek a gazdagép által csatlakoztatott köteteknek, beleértve az iSCSI-és StorSimple-eszközök használatával felderített köteteket is. |
| kötet tárolója |Kötetek csoportosítása és azok beállításai. A StorSimple-eszköz összes kötete mennyiségi tárolóba van csoportosítva. A mennyiségi tároló beállításai közé tartoznak a Storage-fiókok, a felhőbe a társított titkosítási kulcsokkal továbbított adatok titkosítási beállításai, valamint a felhővel kapcsolatos műveletek során felhasznált sávszélesség. |
| kötet csoport |A StorSimple Snapshot Managerban a kötetek csoportja a biztonsági másolatok feldolgozásának megkönnyítésére konfigurált kötetek gyűjteménye. |
| Kötet árnyékmásolata szolgáltatás (VSS) |Egy Windows Server operációs rendszer szolgáltatás, amely megkönnyíti az alkalmazások konzisztenciáját a VSS-t támogató alkalmazásokkal való kommunikációval, hogy összehangolja a növekményes pillanatképek létrehozását. A VSS biztosítja, hogy az alkalmazások a pillanatképek elkészítésekor átmenetileg inaktívak legyenek. |
| Windows PowerShell StorSimple-bővítménye |A StorSimple-eszköz üzemeltetéséhez és kezeléséhez használt Windows PowerShell-alapú parancssori felület. A Windows PowerShell néhány alapszintű funkciójának fenntartása mellett ez az illesztő további dedikált parancsmagokkal rendelkezik, amelyek a StorSimple-eszközök felügyeletére irányulnak. |

## <a name="next-steps"></a>Következő lépések
A [StorSimple biztonság](storsimple-8000-security.md)megismerése.