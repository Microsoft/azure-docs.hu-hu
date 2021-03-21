---
title: Mi az a StorSimple Snapshot Manager? | Microsoft Docs
description: Ismerteti a StorSimple Snapshot Manager, annak architektúráját és funkcióit.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2214f085c30419cefb3f6f84139d5592873173f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017984"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Bevezetés a StorSimple Snapshot Manager

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modul, amely leegyszerűsíti az adatvédelmet és a biztonsági mentési felügyeletet egy Microsoft Azure StorSimple környezetben. A StorSimple Snapshot Manager segítségével egyetlen integrált tárolási megoldásként kezelheti az adatközpontban és a felhőben tárolt Microsoft Azure StorSimple-adatmennyiségeket, így egyszerűsítve a biztonsági mentési folyamatokat, és csökkentheti a költségeket.

Ez az áttekintés bemutatja a StorSimple Snapshot Manager, ismerteti a funkcióit, és ismerteti a szerepkörét Microsoft Azure StorSimpleban. 

A teljes Microsoft Azure StorSimple rendszer áttekintéséhez, beleértve a StorSimple-eszközt, StorSimple Manager szolgáltatást, a StorSimple Snapshot Manager és a SharePoint StorSimple-adapterét, lásd [: StorSimple 8000 sorozat: hibrid felhőalapú tárolási megoldás](storsimple-overview.md). 

> [!NOTE]
> * A StorSimple Snapshot Manager nem használhatók Microsoft Azure StorSimple virtuális tömbök (más néven StorSimple-alapú virtuális eszközök) kezelésére.
> * Ha a StorSimple 2. frissítését tervezi telepíteni a StorSimple-eszközön, töltse le a Snapshot Manager StorSimple legújabb verzióját, és telepítse a **StorSimple Update 2 telepítése előtt**. A StorSimple Snapshot Manager legújabb verziója visszamenőlegesen kompatibilis, és a Microsoft Azure StorSimple összes kiadott verziójával működik. Ha a StorSimple Snapshot Manager korábbi verzióját használja, frissítenie kell (az új verzió telepítése előtt nem kell eltávolítania a korábbi verziót).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager célja és architektúrája
A StorSimple Snapshot Manager egy központi felügyeleti konzolt biztosít, amellyel konzisztens, időponthoz kötött biztonsági másolatokat hozhat létre a helyi és a Felhőbeli adatmennyiségekről. A konzollal például a következőket végezheti el:

* Kötetek konfigurálása, biztonsági mentése és törlése.
* Konfigurálja a mennyiségi csoportokat annak biztosítására, hogy a biztonsági másolatok az alkalmazások konzisztensek legyenek.
* Biztonsági mentési házirendek kezelése, hogy az adatbiztonsági mentés előre meghatározott ütemterv szerint történjen.
* Helyi és Felhőbeli pillanatképeket hozhat létre, amelyek a felhőben tárolhatók, és a katasztrófa utáni helyreállításhoz használhatók.

A StorSimple Snapshot Manager lekéri a VSS-szolgáltatónál regisztrált alkalmazások listáját a gazdagépen. Ezután az alkalmazással konzisztens biztonsági másolatok létrehozásához ellenőrzi az alkalmazás által használt köteteket, és javaslatot tesz a kötet-csoportok konfigurálására. A StorSimple Snapshot Manager ezeket a köteteket használja az alkalmazás-konzisztens biztonsági másolatok létrehozásához. (Az alkalmazás konzisztenciája akkor áll fenn, ha az összes kapcsolódó fájl és adatbázis szinkronizálva van, és az alkalmazás valódi állapotát jelzi egy adott időpontban.) 

A StorSimple Snapshot Manager a biztonsági mentések növekményes Pillanatképek formájában jelennek meg, amelyek a legutóbbi biztonsági mentés óta csak a változásokat rögzítik. Ennek eredményeképpen a biztonsági másolatok kevesebb tárterületet igényelnek, és gyorsan létrehozhatók és visszaállíthatók. A StorSimple Snapshot Manager a Windows Kötet árnyékmásolata szolgáltatás (VSS) használatával biztosítja, hogy a pillanatképek alkalmazás-konzisztens adatmennyiséget rögzítsen. (További információért látogasson el az integráció a Windows Kötet árnyékmásolata szolgáltatás szakaszra.) A StorSimple Snapshot Manager segítségével biztonsági mentési ütemezéseket hozhat létre, vagy igény szerint azonnal készíthet biztonsági másolatokat. Ha vissza kell állítania az adatok biztonsági mentését, a StorSimple Snapshot Manager lehetővé teszi a helyi vagy Felhőbeli Pillanatképek katalógusból való kiválasztását. Az Azure StorSimple csak azokat az adatok visszaállítását állítja helyre, amelyekre szükség van, ami megakadályozza az adatok rendelkezésre állásának késleltetését a visszaállítási műveletek során.)

![StorSimple Snapshot Manager architektúra](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**StorSimple Snapshot Manager architektúra** 

## <a name="support-for-multiple-volume-types"></a>Több mennyiségi típus támogatása
A StorSimple Snapshot Manager a következő típusú kötetek konfigurálására és biztonsági mentésére használható: 

* **Alapszintű kötetek** – az alapszintű kötet egy alapszintű lemez egyetlen partíciója. 
* **Egyszerű kötetek** – az egyszerű kötet egy dinamikus kötet, amely egyetlen dinamikus lemezről tartalmaz lemezterületet. Egy egyszerű kötet egy lemez egyetlen régiójából vagy több, ugyanazon a lemezen összekapcsolt régióból áll. (Csak dinamikus lemezeken hozhat létre egyszerű köteteket.) Az egyszerű kötetek nem hibatűrők.
* **Dinamikus kötetek** – a dinamikus kötet egy dinamikus lemezen létrehozott kötet. A dinamikus lemezek egy adatbázis használatával követik nyomon a számítógép dinamikus lemezei által tárolt kötetekkel kapcsolatos információkat. 
* **Dinamikus kötetek tükrözéssel** – a tükrözött dinamikus KÖTETEK a RAID 1 architektúrára épülnek. A RAID 1 esetében az azonos adatbevitel két vagy több lemezen történik, tükrözött készlet létrehozásával. Ezután egy olvasási kérést bármely olyan lemez képes kezelni, amely tartalmazza a kért adattípust.
* **Fürt megosztott kötetei** – a fürt megosztott köteteivel (CSV) a feladatátvevő fürt több csomópontja egyszerre tud olvasni vagy írni ugyanahhoz a lemezhez. Az egyik csomópontról egy másik csomópontra történő feladatátvétel gyorsan elvégezhető, anélkül, hogy módosítani kellene a meghajtó tulajdonjogát vagy a csatlakoztatást, a leválasztást és a kötet eltávolítását. 

> [!IMPORTANT]
> Ne keverje össze a CSV és a nem CSV ugyanabban a pillanatképben. A CSV és a nem CSV egy pillanatképben való keverése nem támogatott. 
> 
> 

A StorSimple Snapshot Manager használatával visszaállíthatja a teljes kötet-csoportokat, vagy megoszthatja az egyes köteteket, és helyreállíthatja az egyes fájlokat.

* [Kötetek és mennyiségi csoportok](#volumes-and-volume-groups) 
* [Biztonsági mentési típusok és biztonsági mentési szabályzatok](#backup-types-and-backup-policies) 

További információ a StorSimple Snapshot Manager szolgáltatásairól és használatáról: a [StorSimple Snapshot Manager felhasználói felülete](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Kötetek és mennyiségi csoportok
A StorSimple Snapshot Manager segítségével kötetek hozhatók létre, majd konfigurálhatók a kötetek csoportjaiba. 

A StorSimple Snapshot Manager a mennyiségi csoportok használatával hozza létre az alkalmazás-konzisztens biztonsági másolatokat. Az alkalmazás konzisztenciája akkor áll fenn, ha minden kapcsolódó fájl és adatbázis szinkronizálva van, és egy adott időpontban egy alkalmazás valódi állapotát jelöli. A mennyiségi csoportok (amelyek más néven *konzisztencia-csoportok*) a biztonsági mentési vagy visszaállítási feladatok alapját képezik.

A kötetek csoportjai nem egyeznek a mennyiségi tárolókkal. A mennyiségi tárolók egy vagy több olyan kötetet tartalmaznak, amelyek osztoznak egy felhőalapú Storage-fiókban és más attribútumokon, például a titkosítás és a sávszélesség használatában. Egyetlen mennyiségi tároló legfeljebb 256, dinamikusan kiosztott StorSimple-kötetet tartalmazhat. A mennyiségi tárolókkal kapcsolatos további információkért nyissa [meg a mennyiségi tárolók kezelése](./storsimple-8000-manage-volume-containers.md)című témakört. A kötetek a biztonsági mentési műveletek megkönnyítésére konfigurált kötetek gyűjteményei. Ha két, különböző kötet-tárolóhoz tartozó kötetet választ, helyezze azokat egyetlen kötet csoportba, majd hozzon létre egy biztonsági mentési szabályzatot az adott kötethez, majd minden kötetről készítsen biztonsági mentést a megfelelő méretű tárolóban a megfelelő tárolási fiók használatával.

> [!NOTE]
> Egy mennyiségi csoport összes kötetének egyetlen felhőalapú szolgáltatótól kell származnia.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integráció a Windows Kötet árnyékmásolata szolgáltatás
A StorSimple Snapshot Manager a Windows Kötet árnyékmásolata szolgáltatás (VSS) használatával rögzíti az alkalmazás-konzisztens adatmennyiséget. A VSS a VSS-t támogató alkalmazásokkal folytatott kommunikáció révén megkönnyíti az alkalmazások konzisztenciáját a növekményes Pillanatképek létrehozásának koordinálásához. A VSS gondoskodik arról, hogy az alkalmazások átmenetileg inaktívak vagy nyugalmi legyenek a pillanatképek készítésekor. 

A StorSimple Snapshot Manager a VSS implementálása SQL Server és általános NTFS-kötetekkel működik. A folyamat a következő: 

1. A kérelmező, amely általában egy adatkezelési és védelmi megoldás (például StorSimple Snapshot Manager) vagy egy biztonságimásolat-készítő alkalmazás, meghívja a VSS-t, és kéri, hogy gyűjtsön adatokat a célalkalmazás írója szoftverből.
2. A VSS kapcsolatba lép az író összetevővel az adatleírás lekéréséhez. Az író a biztonsági mentésre kerülő adatok leírását adja vissza. 
3. A VSS jelzi az író számára, hogy előkészítse az alkalmazást a biztonsági mentéshez. Az író a nyitott tranzakciók, a tranzakciónaplók frissítése és így tovább, majd értesíti a VSS-t a biztonsági mentéshez.
4. A VSS arra utasítja az íróot, hogy ideiglenesen állítsa le az alkalmazás adattárait, és győződjön meg arról, hogy az árnyékmásolat létrehozásakor a köteten nem történik az adatírás. Ez a lépés biztosítja az adatkonzisztenciaot, és legfeljebb 60 másodpercet vesz igénybe.
5. A VSS arra utasítja a szolgáltatót, hogy hozza létre az árnyékmásolatot. A szoftver-vagy hardver-alapú szolgáltatók, a jelenleg futó kötetek kezelése, valamint igény szerint árnyékmásolatok készítése. A szolgáltató létrehozza az árnyékmásolatot, és a befejezéskor értesíti a VSS-t.
6. A VSS kapcsolatba lép az írómal, hogy értesítse az I/O-t folytató alkalmazást, és ellenőrizze, hogy az I/O felfüggesztése sikeresen megtörtént-e az árnyékmásolat létrehozásakor. 
7. Ha a másolás sikeres volt, a VSS visszaadja a másolás helyét a kérelmezőnek. 
8. Ha az árnyékmásolat létrehozása közben megírta az adatírást, a biztonsági mentés inkonzisztens lesz. A VSS törli az árnyékmásolat-példányt, és értesíti a kérelmezőt. A kérelmező elvégezheti a biztonsági mentési folyamat automatikus megismétlését, vagy értesítheti a rendszergazdát, hogy később újra próbálkozik.

Lásd az alábbi ábrát.

![VSS-folyamat](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Windows Kötet árnyékmásolata szolgáltatás folyamat** 

## <a name="backup-types-and-backup-policies"></a>Biztonsági mentési típusok és biztonsági mentési szabályzatok
A StorSimple Snapshot Manager segítségével biztonsági mentést készíthet, és helyileg és a felhőben is tárolhatja azokat. Az StorSimple Snapshot Manager használatával azonnal biztonsági másolatot készíthet az adatairól, vagy biztonsági mentési szabályzattal létrehozhat egy ütemezést, amely automatikusan készít biztonsági mentést. A biztonsági mentési szabályzatok segítségével megadhatja, hogy hány pillanatképet őrizzen meg a rendszer. 

### <a name="backup-types"></a>Biztonsági másolatok típusai
A StorSimple Snapshot Manager a következő típusú biztonsági másolatok létrehozására használható:

* **Helyi Pillanatképek** – a helyi Pillanatképek a StorSimple-eszközön tárolt mennyiségi adatmennyiségek időponthoz kötött példányai. Az ilyen típusú biztonsági másolatok általában gyorsan létrehozhatók és visszaállíthatók. Helyi biztonsági másolat készítésekor helyi pillanatképet is használhat.
* **Felhőbeli Pillanatképek** – a felhőalapú Pillanatképek a felhőben tárolt mennyiségi adatmennyiségek időponthoz kötődő példányai. A Felhőbeli Pillanatképek egy másik, nem helyszíni tárolási rendszeren replikált pillanatképnek felelnek meg. A Felhőbeli Pillanatképek különösen hasznosak a vész-helyreállítási helyzetekben.

### <a name="on-demand-and-scheduled-backups"></a>Igény szerinti és ütemezett biztonsági mentések
A StorSimple Snapshot Manager használatával azonnal létrehozhat egy egyszeri biztonsági mentést, vagy használhat biztonsági mentési szabályzatot is az ismétlődő biztonsági mentési műveletek elindításához.

A biztonsági mentési szabályzat olyan automatizált szabályok összessége, amelyek segítségével rendszeres biztonsági mentéseket ütemezhet. A biztonsági mentési szabályzat lehetővé teszi, hogy meghatározza a gyakoriságot és a paramétereket egy adott mennyiségi csoport pillanatképének készítéséhez. Házirendekkel megadhatja a kezdő és a lejárati dátumokat, az időpontokat, a gyakoriságokat és a megőrzési követelményeket mind a helyi, mind a Felhőbeli pillanatképekhez A szabályzatot a definiálását követően azonnal alkalmazza a rendszer. 

Ha szükséges, a StorSimple Snapshot Manager használatával konfigurálhatja vagy újrakonfigurálhatja a biztonsági mentési házirendeket. 

A következő információkat kell konfigurálnia minden létrehozott biztonsági mentési szabályzathoz:

* **Name (név** ) – a kiválasztott biztonsági mentési szabályzat egyedi neve.
* **Type (típus** ) – a biztonsági mentési szabályzat típusa; helyi pillanatkép vagy Felhőbeli pillanatkép.
* **Kötet csoport** – az a kötet, amelyhez a kiválasztott biztonsági mentési szabályzat hozzá van rendelve.
* **Megőrzés** – a megőrizni kívánt biztonsági másolatok száma. Ha bejelöli a **minden** biztonsági másolatot, a rendszer minden biztonsági másolatot megtart, amíg a biztonsági másolatok maximális száma elérte a kötetet, és ekkor a házirend sikertelen lesz, és hibaüzenetet fog eredményezni. Másik lehetőségként megadhatja a megőrizni kívánt biztonsági másolatok számát (1 és 64 között).
* **Date (dátum** ) – a biztonsági mentési szabályzat létrehozásának dátuma.

A biztonsági mentési szabályzatok konfigurálásával kapcsolatos információkért lépjen a [StorSimple Snapshot Manager használatára a biztonsági mentési házirendek létrehozásához és kezeléséhez](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Biztonsági mentési feladatok figyelése és kezelése
A StorSimple Snapshot Manager segítségével figyelheti és kezelheti a közelgő, ütemezett és befejezett biztonsági mentési feladatokat. Emellett a StorSimple Snapshot Manager katalógusa akár 64 befejezett biztonsági mentést is biztosít. A katalógus használatával kötetek vagy egyedi fájlok kereshetők és állíthatók vissza. 

További információ a biztonsági mentési feladatok figyeléséről: a [StorSimple Snapshot Manager használata a biztonsági mentési feladatok megtekintéséhez és kezeléséhez](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple Snapshot Manager használatáról a StorSimple-megoldás felügyeletéhez](storsimple-snapshot-manager-admin.md).
* Töltse le a [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).