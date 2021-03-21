---
title: Microsoft Azure StorSimple virtuális tömb áttekintése
description: Ismerteti a StorSimple virtuális tömböt, amely egy integrált tárolási megoldás, amely a helyszíni virtuális tömb és a felhőalapú tároló Microsoft Azure közötti tárolási feladatokat kezeli.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 6d7d8c37d0e7ffacb3221a4f30a96ef6cccda819
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104674444"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>A StorSimple virtuális tömb bemutatása

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Áttekintés

A Microsoft Azure StorSimple Virtual Array egy integrált tárolási megoldás, amely egy olyan helyszíni virtuális tömb tárolási feladatait kezeli, amely egy hypervisorban és Microsoft Azure felhőalapú tárolóban fut. A virtuális tömb egy hatékony, költséghatékony és könnyen kezelhető fájlkiszolgáló-vagy iSCSI-kiszolgálói megoldás, amely kiküszöböli a vállalati tárterülethez és az adatvédelemhez kapcsolódó számos problémát és költséget. A virtuális tömb különösen jól illeszkedik a ritkán használt archiválási adatok tárolásához.

Ez a cikk áttekintést nyújt a virtuális tömbről – Íme néhány további erőforrás:

* Az ajánlott eljárásokért lásd: [StorSimple Virtual Array – ajánlott eljárások](storsimple-ova-best-practices.md).
* Az StorSimple 8000 sorozatú eszközök áttekintéséhez nyissa meg a [StorSimple 8000 Series: a hibrid felhőalapú megoldást](storsimple-overview.md).
* További információ a StorSimple 5000/7000 sorozatú eszközökről: [StorSimple online súgó](http://onlinehelp.storsimple.com/).

A virtuális tömb támogatja az iSCSI-vagy kiszolgálói üzenetblokk (SMB) protokollt. Ez a meglévő hypervisor-infrastruktúrán fut, és a felhő, a Felhőbeli biztonsági mentés, a gyors visszaállítás, az elemszintű helyreállítás és a vész-helyreállítási funkciók szintjére nyújt lehetőséget.

A következő táblázat összefoglalja a StorSimple virtuális tömb fontos funkcióit.

| Szolgáltatás | StorSimple Virtual Array |
| --- | --- |
| Telepítési követelmények |Virtualizációs infrastruktúrát (Hyper-V vagy VMware) használ |
| Rendelkezésre állás |Egyetlen csomópont |
| Teljes kapacitás (beleértve a felhőt is) |Akár 64 TB felhasználható kapacitás virtuális tömbben |
| Helyi kapacitás |390 GB és 6,4 TB felhasználható kapacitás virtuális tömbben (500 GB – 8 TB lemezterületet kell kiépíteni) |
| Natív protokollok |iSCSI vagy SMB |
| Helyreállítási időre vonatkozó célkitűzés (RTO) |iSCSI: kevesebb, mint 2 perc a mérettől függetlenül |
| Helyreállítási időkorlát (RPO) |Napi biztonsági mentések és igény szerinti biztonsági mentések |
| Tárolási rétegek |A hő-hozzárendelés használatával határozza meg, hogy milyen szintű vagy kimenő adatkeret |
| Támogatás |A szállító által támogatott virtualizációs infrastruktúra |
| Teljesítmény |Az alapul szolgáló infrastruktúrától függ |
| Adatmobilitás |Visszaállítható ugyanarra az eszközre, vagy az elemszintű helyreállítást (fájlkiszolgáló) |
| Tárolási szintek |Helyi hypervisor tároló és felhő |
| Megosztás mérete |Rétegű: legfeljebb 20 TB; helyileg rögzítve: legfeljebb 2 TB |
| Kötet mérete |Rétegű: 500 GB – 5 TB; helyileg rögzített: 50 GB – 200 GB <br> A többszintes kötetek maximális helyi foglalása 200 GB. |
| Pillanatképek |Összeomlás-konzisztens |
| Elemszintű helyreállítás |Igen a felhasználók visszaállíthatják a megosztásokat |

## <a name="why-use-storsimple"></a>Miért érdemes használni a StorSimple?

A StorSimple percek alatt összekapcsolja a felhasználókat és a kiszolgálókat az Azure Storage-ba, az alkalmazások módosítása nélkül.

Az alábbi táblázat a StorSimple Virtual Array megoldás által biztosított főbb előnyöket ismerteti.

| Szolgáltatás | Előny |
| --- | --- |
| Transzparens integráció |A virtuális tömb támogatja az iSCSI-t vagy az SMB protokollt. A helyi és a felhőalapú csomag közötti adatáthelyezés zökkenőmentes és átlátható a felhasználó számára. |
| Csökkentett tárolási költségek |A StorSimple-mel elegendő helyi tárterületet kell kiépíteni, hogy megfeleljen a leggyakrabban használt meleg adatokra vonatkozó jelenlegi igényeknek. A tárolási igények növekedésével a StorSimple rétegekben a Felhőbeli tárolás költséghatékony. Az adatok deduplikálása és tömörítése a felhőbe való küldés előtt a tárolási követelmények és költségek további csökkentése érdekében. |
| Egyszerűsített tárolók kezelése |A StorSimple központosított felügyeletet biztosít a felhőben a StorSimple Eszközkezelő használatával több eszköz kezeléséhez. |
| A vész-helyreállítás és-megfelelőség javítása |A StorSimple megkönnyíti a vész-helyreállítást azáltal, hogy azonnal visszaállítja a metaadatokat, és szükség szerint helyreállítja az adatokat. A normál műveletek a minimális megszakítással is folytatódnak. |
| Adatmobilitás |A felhőbe felhasználható adatok helyreállítási és áttelepítési célból más helyekről is elérhetők. Csak az eredeti virtuális tömbre állíthatja vissza az adatkészletet. A vész-helyreállítási funkciók használatával azonban a teljes virtuális tömböt egy másik virtuális tömbre állíthatja vissza. |



## <a name="storsimple-workload-summary"></a>StorSimple munkaterhelés összegzése

Az alábbi táblázat a támogatott StorSimple-számítási feladatok összefoglalóját mutatja be.

|Eset     |Számítási feladat     |Támogatott      |Korlátozások               | Alkalmazható verziók|
|-------------|-------------|---------------|---------------------------|--------------------|
|Távoli iroda/fiókiroda (ROBO)  |Fájlmegosztás     |Yes      |Lásd: [a fájlkiszolgáló maximális korlátai](storsimple-ova-limits.md).<br></br>Tekintse [meg a támogatott SMB-verziók rendszerkövetelményeit](storsimple-ova-system-requirements.md).| Az összes verzió     |
|Felhőbeli archiválás  |Archiválási fájlmegosztás     |Yes      |Lásd: [a fájlkiszolgáló maximális korlátai](storsimple-ova-limits.md).<br></br>Tekintse [meg a támogatott SMB-verziók rendszerkövetelményeit](storsimple-ova-system-requirements.md).| Az összes verzió     |

A StorSimple Virtual Array a ritkán használt adatokhoz ajánlott. Míg a virtuális tömb helyi gyorsítótárral rendelkezik a teljesítmény növelése érdekében, a felhasználóknak feltételezni kell, hogy az eszköz-szolgáltatások fájljai a legalacsonyabb tárolási szinten (a felhőben) találhatók. Minden egyes virtuális tömb körülbelül 100 Mbps sebességgel tud írni és olvasni az Azure Storage-ba. Ez a hivatkozás az eszközre érkező összes kérelem között megoszlik, és az alábbi ábrán látható módon szűk keresztmetszetet jelenthet.

![Felhőbeli archiválás](./media/storsimple-ova-overview/cloud-archiving.png)

Ha több egyidejű felhasználó fér hozzá a virtuális tömbhöz, mind osztoznak a kapcsolaton az Azure-ban, ami alacsonyabb teljesítményt eredményez. Felhasználónként nincs garantált teljesítmény, és az eszköz a megérkezéskor dolgozza fel az egyes kérelmeket.

A StorSimple virtuális tömb nem alkalmas magas rendelkezésre állást igénylő munkaterhelések esetén. A virtuális tömb egy egycsomópontos eszköz, amely leállást tapasztal a szoftverfrissítések telepítésekor. A rendszergazdáknak évente legfeljebb 3-4 alkalommal kell megtervezniük a karbantartási időszakot.

## <a name="workflows"></a>Munkafolyamatok

A StorSimple virtuális tömb különösen a következő munkafolyamatokhoz alkalmas:

* [Felhőalapú tárolók kezelése](#cloud-based-storage-management)
* [Hely – független biztonsági mentés](#location-independent-backup)
* [Adatvédelem és vész-helyreállítás](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Felhőalapú tárolók kezelése
A Azure Portal futtatott StorSimple Eszközkezelő szolgáltatással kezelheti a több eszközön és több helyen tárolt adatkezelési szolgáltatásokat. Ez különösen az elosztott ág-forgatókönyvekben hasznos. A virtuális tömbök és a fizikai StorSimple-eszközök kezeléséhez létre kell hoznia a StorSimple Eszközkezelő szolgáltatás különálló példányait. A virtuális tömb mostantól az új Azure Portal használja a klasszikus Azure portál helyett.<!--Is the "now" element still in date? Could it go at this point? Just checking.-->

![felhőalapú tárolók kezelése](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Hely – független biztonsági mentés
A virtuális tömbben a Felhőbeli Pillanatképek biztosítják a kötetek vagy megosztások helytől független, időponthoz való másolását. A Felhőbeli Pillanatképek alapértelmezés szerint engedélyezve vannak, és nem tilthatók le. Az összes kötetet és megosztást egy napi biztonsági mentési szabályzattal egy időben készíti el a rendszer, és szükség esetén további ad hoc biztonsági mentést is igénybe vehet.

### <a name="data-protection-and-disaster-recovery"></a>Adatvédelem és vész-helyreállítás
A virtuális tömb a következő adatvédelmi és vész-helyreállítási forgatókönyveket támogatja:

* **Kötet vagy megosztás visszaállítása** – a kötet vagy megosztás helyreállításához használja a visszaállítás új munkafolyamatként lehetőséget. Ezzel a módszerrel állíthatja helyre a teljes kötetet vagy megosztást.
* **Elemszintű helyreállítás** – a megosztások lehetővé teszik a legutóbbi biztonsági másolatok egyszerűsített elérését. Egyszerűen helyreállíthat egy egyedi fájlt a felhőben elérhető speciális *. backup* mappából. Ez a visszaállítási funkció felhasználó által vezérelt, és nincs szükség rendszergazdai beavatkozásra.
* Vész- **helyreállítás** – a feladatátvételi képesség használatával helyreállíthatja az összes kötetet vagy megosztást egy új virtuális tömbben. Hozza létre az új virtuális tömböt, és regisztrálja a StorSimple Eszközkezelő szolgáltatásban, majd hajtsa végre az eredeti virtuális tömb feladatátvételét. Az új virtuális tömb ezután a kiépített erőforrásokat fogja feltételezni.

## <a name="storsimple-virtual-array-components"></a>StorSimple virtuális tömb összetevői

A virtuális tömb a következő összetevőket tartalmazza:

* [Virtual Array](#virtual-array) – egy hibrid felhőalapú tárolóeszköz, amely egy virtualizált környezetben vagy hypervisorban kiépített virtuális gépen alapul.
* [StorSimple Eszközkezelő Service](#storsimple-device-manager-service) – a Azure Portal egy bővítménye, amely lehetővé teszi egy vagy több StorSimple-eszköz felügyeletét egyetlen webes felületről, amelyek különböző földrajzi helyekről érhetők el. A StorSimple Eszközkezelő szolgáltatással szolgáltatások hozhatók létre és kezelhetők, megtekinthetők és kezelhetők az eszközök és a riasztások, valamint kezelhetők a kötetek, a megosztások és a meglévő Pillanatképek.
* [Helyi webes felhasználói felület](#local-web-user-interface) – egy WEBalapú felhasználói felület, amely úgy van konfigurálva, hogy az eszköz csatlakozhasson a helyi hálózathoz, majd regisztrálja az eszközt a StorSimple Eszközkezelő szolgáltatással. 
* [Parancssori felület](#command-line-interface) – egy Windows PowerShell-felület, amellyel egy támogatási munkamenet indítható el a virtuális tömbben.
  Az alábbi szakaszok részletesebben ismertetik az egyes összetevőket, és elmagyarázza, hogyan rendezi a megoldás az adatokat, lefoglalja a tárterületet, és megkönnyíti a tárterület-kezelést és az adatvédelmet.

### <a name="virtual-array"></a>Virtuális tömb

A virtuális tömb egy egycsomópontos tárolási megoldás, amely elsődleges tárterületet biztosít, kezeli a Felhőbeli tárolással folytatott kommunikációt, és segít az eszközön tárolt összes adat biztonságának és titkosságának biztosításában.

A virtuális tömb elérhető egy olyan modellben, amely letölthető. A virtuális tömb maximális kapacitása 6,4 TB az eszközön (a mögöttes tárolási követelmény 8 TB) és 64 TB, beleértve a felhőalapú tárolást.

A virtuális tömb a következő funkciókkal rendelkezik:

* Költséghatékony. A meglévő virtualizációs infrastruktúra használatát teszi lehetővé, és a meglévő Hyper-V-vagy VMware hypervisoron is üzembe helyezhető.
* Az adatközpontban található, és konfigurálható iSCSI-kiszolgálóként vagy fájlkiszolgálóként.
* Integrálva van a felhővel.
* A biztonsági mentések a felhőben tárolódnak, ami meggyorsíthatja a vész-helyreállítást, és egyszerűsítheti az elemszintű helyreállítást (ILR).
* A virtuális tömbre is alkalmazhat frissítéseket, ugyanúgy, ahogy azt egy fizikai eszközre alkalmazza.

> [!NOTE]
> Nem lehet kibontani egy virtuális tömböt. Ezért fontos a megfelelő tárterület kiépítése a virtuális tömb létrehozásakor.

### <a name="storsimple-device-manager-service"></a>StorSimple-eszközkezelő szolgáltatás

A Microsoft Azure StorSimple webalapú felhasználói felületet biztosít a StorSimple Eszközkezelő szolgáltatáshoz, amely lehetővé teszi az StorSimple-tárolók központi felügyeletét. A következő feladatok elvégzéséhez használhatja a StorSimple Eszközkezelő szolgáltatást:

* Több StorSimple virtuális tömb kezelése egyetlen szolgáltatásból.
* StorSimple virtuális tömbök biztonsági beállításainak konfigurálása és kezelése. (A felhőben való titkosítás Microsoft Azure API-kkal függ.)
* A Storage-fiók hitelesítő adatainak és tulajdonságainak konfigurálása.
* Kötetek és megosztások konfigurálása és kezelése.
* Kötetek és megosztások biztonsági mentése és visszaállítása.
* A teljesítmény figyelése.
* Tekintse át a rendszerbeállításokat, és azonosítsa a lehetséges problémákat.

A virtuális tömb napi felügyeletét a StorSimple Eszközkezelő szolgáltatással végezheti el.

További információért látogasson el [a StorSimple Eszközkezelő szolgáltatás használatára a StorSimple-eszköz felügyeletéhez](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Helyi webes felhasználói felület

A virtuális tömb tartalmaz egy webalapú felhasználói felületet, amely az eszköz egyszeri konfigurálásához és a StorSimple Eszközkezelő szolgáltatással való regisztrálásához használható. Használhatja a virtuális tömb leállítására és újraindítására, diagnosztikai tesztek futtatására, szoftver frissítésére, az eszköz rendszergazdai jelszavának megváltoztatására, a rendszernaplók megtekintésére és a Microsoft ügyfélszolgálata a szolgáltatási kérések fájlba való kapcsolódására.

A webalapú felhasználói felület használatáról a [webes felhasználói felület használata a StorSimple virtuális tömb felügyeletéhez](storsimple-ova-web-ui-admin.md)című témakörben olvashat bővebben.

### <a name="command-line-interface"></a>Parancssori felület

A mellékelt Windows PowerShell-felület lehetővé teszi, hogy támogatási munkamenetet kezdeményezzen Microsoft ügyfélszolgálata segítségével, így a virtuális tömbben esetlegesen felmerülő problémák elhárításához és megoldásához segítséget nyújthat.

## <a name="storage-management-technologies"></a>Storage-felügyeleti technológiák

A virtuális tömb és más összetevők mellett a StorSimple-megoldás a következő szoftver-technológiákat használja a fontos adatok gyors eléréséhez, a tárterület-felhasználás csökkentéséhez és a virtuális tömbben tárolt adatok védelme érdekében.

* [Automatikus tárolási rétegek](#automatic-storage-tiering) 
* [Helyileg rögzített megosztások és kötetek](#locally-pinned-shares-and-volumes)
* Az adatrétegek és a felhőbe történő biztonsági mentés megkettőzése és tömörítése 
* [Ütemezett és igény szerinti biztonsági mentések](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatikus tárolási rétegek
A virtuális tömb egy új, a virtuális tömbben és a felhőben tárolt adatkezelési mechanizmust használ. Csak két szint létezik: a helyi virtuális tömb és az Azure Cloud Storage. A StorSimple Virtual Array automatikusan átrendezi az adatokat a rétegekre egy hő-Térkép alapján, amely nyomon követi az aktuális használatot, az életkort és a más adatokhoz való kapcsolatokat. A legtöbb aktív (legforróbb) adatmennyiség helyileg tárolódik, míg a rendszer a kevésbé aktív és inaktív adatmennyiségeket automatikusan áttelepíti a felhőbe. (Az összes biztonsági mentés a felhőben történik.) A StorSimple módosítja és átrendezi az adatokat és a tárolási hozzárendeléseket a használati minták változásakor. Előfordulhat például, hogy egyes információk kevésbé lesznek aktívak az idő múlásával. Mivel egyre kevésbé aktív, a felhőbe kerül. Ha ugyanezek az adatelemek ismét aktívvá válnak, a rendszer a Storage tömbbe van bontva.

Egy adott rétegű megosztás vagy kötet adatai garantáltak a saját helyi rétegeinek (a megosztás vagy a kötet teljes kiépített területének körülbelül 10 százaléka). Míg ez csökkenti a rendelkezésre álló tárhelyet a virtuális tömbben az adott megosztás vagy kötet számára, biztosítja, hogy az egyik megosztásra vagy kötetre vonatkozó rétegek nem lesznek hatással a más megosztások vagy kötetek rétegbeli igényeire. Így az egyik megosztáson vagy köteten egy nagyon elfoglalt munkaterhelés nem kényszerítheti az összes többi számítási feladatot a felhőbe.

Az iSCSI számára létrehozott rétegű kötetek maximális helyi foglalása 200 GB, a kötet méretétől függetlenül.

![automatikus tárolási rétegek](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> A kötetet helyileg rögzítettként is megadhatja, ebben az esetben az adatforgalom a virtuális tömbben marad, és a rendszer soha nem a felhőbe van bontva. További információért lépjen a [helyileg rögzített megosztások és kötetek](#locally-pinned-shares-and-volumes)elemre.

> [!IMPORTANT]
> A StorSimple használatakor ne konvertálja a blobokat archiválásra, még akkor is, ha az eszköz fázisa folyamatban van. Az adatok az eszközről való lekéréséhez a blobokat a gyakori vagy a ritka elérésű típusra kell kihasználnia, amely jelentős költségeket eredményez.


### <a name="locally-pinned-shares-and-volumes"></a>Helyileg rögzített megosztások és kötetek

A megfelelő megosztásokat és köteteket helyileg rögzítettként hozhatja létre. Ez a funkció biztosítja, hogy a kritikus alkalmazások által igényelt adatműveletek a virtuális tömbben maradjanak, és soha ne legyenek a felhőbe bontva. A helyileg rögzített megosztások és kötetek a következő funkciókkal rendelkeznek:

* Nem tartoznak a Felhőbeli késések vagy a kapcsolódási problémák.
* Továbbra is kihasználják a StorSimple Cloud Backup és a vész-helyreállítási funkciók előnyeit.

A helyileg rögzített megosztásokat vagy köteteket beállíthatja úgy, hogy az a lehető leggyorsabban rögzítse a lépcsőzetesen rögzített megosztást vagy kötetet. 

További információ a helyileg rögzített kötetekről: [a StorSimple Eszközkezelő szolgáltatás használata a kötetek kezelésére](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Az adatrétegek és a felhőbe történő biztonsági mentés megkettőzése és tömörítése

A StorSimple a Felhőbeli tárolási követelmények további csökkentése érdekében a deduplikálás és az adattömörítést használja. A deduplikálás csökkenti a tárolt adatkészletben lévő redundancia megszüntetésével a tárolt adatmennyiséget. Ahogy az információ megváltozik, a StorSimple figyelmen kívül hagyja a változatlan adatokat, és csak a módosításokat rögzíti. Emellett a StorSimple az ismétlődő információk azonosításával és eltávolításával csökkenti a tárolt adatok mennyiségét.

> [!NOTE]
> A virtuális tömbben tárolt adathalmazok nincsenek deduplikálva vagy tömörítve. Az összes ismétlődés és tömörítés közvetlenül az adatfelhőbe való továbbítás előtt történik.


### <a name="scheduled-and-on-demand-backups"></a>Ütemezett és igény szerinti biztonsági mentések

A StorSimple adatvédelmi funkciói lehetővé teszik az igény szerinti biztonsági mentések létrehozását. Emellett az alapértelmezett biztonsági mentési ütemterv biztosítja, hogy naponta készítsen biztonsági másolatot az adatairól. A biztonsági mentések a felhőben tárolt növekményes Pillanatképek formájában készülnek. A pillanatképek, amelyek csak a legutóbbi biztonsági mentés óta végrehajtott módosításokat rögzítik, gyorsan létrehozhatók és visszaállíthatók. Ezek a pillanatképek kritikus fontosságúak lehetnek a vész-helyreállítási forgatókönyvekben, mert a másodlagos tárolási rendszereket (például szalagos biztonsági mentést) cserélik le, és szükség esetén lehetővé teszik az adatok adatközpontba vagy másodlagos helyre történő visszaállítását.

## <a name="managing-personal-information"></a>Személyes adatok kezelése

A virtuális adatsorozatok StorSimple Eszközkezelő két fő példányban gyűjt személyes adatokat:
 - Riasztási felhasználói beállítások, ahol a felhasználók e-mail-címei konfigurálva vannak. Ezeket az információkat a rendszergazda törölheti. 
 - Azok a felhasználók, akik hozzáférhetnek a megosztáson lévő információhoz. Megjelenik a megosztási adatelérést elérő felhasználók listája, és exportálható. A rendszer törli a listát a megosztás törlésekor.

További információkért tekintse meg a [Microsoft adatvédelmi szabályzatát a Trust Centerben](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [készítheti elő a virtuális tömb portált](storsimple-virtual-array-deploy1-portal-prep.md).
