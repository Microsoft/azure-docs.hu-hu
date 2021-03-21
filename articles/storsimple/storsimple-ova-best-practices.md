---
title: Ajánlott eljárások a StorSimple Virtual Array szolgáltatáshoz | Microsoft Docs
description: Ismerteti a StorSimple virtuális tömb üzembe helyezésének és kezelésének ajánlott eljárásait.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 71b018da6b54ebf2b45a261378ea521a397159e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94964983"
---
# <a name="storsimple-virtual-array-best-practices"></a>A StorSimple Virtual Array ajánlott eljárásai

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

A Microsoft Azure StorSimple Virtual Array egy integrált tárolási megoldás, amely egy, a hypervisorban és Microsoft Azure felhőalapú tárolóban futó helyszíni virtuális eszköz közötti tárolási feladatokat kezeli. A StorSimple Virtual Array egy hatékony és költséghatékony alternatíva az 8000 sorozat fizikai tömbje számára. A virtuális tömb futhat a meglévő hypervisor-infrastruktúrán, és támogatja az iSCSI-és az SMB-protokollokat is, és kiválóan alkalmas a távoli Office-/fiókirodai forgatókönyvekhez. A StorSimple-megoldásokkal kapcsolatos további információkért [tekintse meg a Microsoft Azure StorSimple áttekintést](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Ez a cikk a StorSimple virtuális tömb kezdeti beállítása, üzembe helyezése és felügyelete során megvalósított ajánlott eljárásokat ismerteti. Ezek az ajánlott eljárások a virtuális tömb beállításához és felügyeletéhez érvényesített irányelveket biztosítanak. Ez a cikk azokat a rendszergazdákat célozza meg, akik a virtuális tömböket üzembe helyezik és kezelik az adatközpontokban.

Javasoljuk, hogy rendszeresen tekintse át az ajánlott eljárásokat, amelyekkel biztosíthatja, hogy az eszköz továbbra is megfeleljen a beállítás vagy a művelet folyamatának változásainak. Ha problémákat tapasztal a virtuális tömbhöz tartozó ajánlott eljárások bevezetése során, forduljon a [Microsoft ügyfélszolgálatahoz](storsimple-virtual-array-log-support-ticket.md) segítségért.

## <a name="configuration-best-practices"></a>Ajánlott eljárások a konfigurációhoz
Ezek az ajánlott eljárások lefedik azokat az irányelveket, amelyeket a virtuális tömbök kezdeti beállítása és üzembe helyezése során kell követni. Ezek az ajánlott eljárások magukban foglalják a virtuális gép kiépítésével, a csoportházirend-beállításokkal, a méretezéssel, a hálózatkezelés beállításával, a Storage-fiókok konfigurálásával, valamint a virtuális tömb megosztásának és köteteinek létrehozásával kapcsolatos eljárásokat. 

### <a name="provisioning"></a>Kiépítés
A StorSimple Virtual Array egy virtuális gép (VM), amely a gazdagép-kiszolgáló hypervisorán (Hyper-V vagy VMware) van kiépítve. A virtuális gép kiépítés esetén győződjön meg arról, hogy a gazdagép elegendő erőforrást tud kiszolgálni. További információkat a tömb kiépítéséhez a [minimális erőforrás-követelmények](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) című témakörben olvashat.

A következő ajánlott eljárások megvalósítása a virtuális tömb kiépítés során:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Virtuális gép típusa** |**2. generáció** A virtuális gép a Windows Server 2012-es vagy újabb verziójával, valamint egy *. vhdx* képpel használható. <br></br> **1. generáció** A virtuális gép Windows Server 2008 vagy újabb, illetve *. vhd* rendszerképpel használható. |A *. VMDK* rendszerképek használata esetén használja a 8-as verziójú virtuális gépet. |
| **Memória típusa** |Konfigurálás **statikus memóriaként**. <br></br> Ne használja a **dinamikus memória** beállítást. | |
| **Adatlemez típusa** |A kiépítés **dinamikusan bővül**.<br></br> A **rögzített méret** hosszú időt vesz igénybe. <br></br> Ne használja a **különbséglemezek** kapcsolót. |Használja a **vékony kiépítési** lehetőséget. |
| **Adatlemez módosítása** |A bővítés vagy a zsugorodás nem engedélyezett. Ennek eredményeképpen az eszközön lévő összes helyi adatbázis elvesztését eredményezi. |A bővítés vagy a zsugorodás nem engedélyezett. Ennek eredményeképpen az eszközön lévő összes helyi adatbázis elvesztését eredményezi. |

### <a name="sizing"></a>Méretezés
A StorSimple virtuális tömb méretezése során vegye figyelembe a következő tényezőket:

* A kötetek és megosztások helyi foglalása. A terület körülbelül 12%-a a helyi szinten van lefoglalva minden egyes kiépített rétegű kötethez vagy megosztáshoz. A terület körülbelül 10%-a a fájlrendszer egy helyileg rögzített kötetéhez is le van foglalva.
* Pillanatkép-terhelés. A helyi szinten körülbelül 15%-os terület a pillanatképek számára van fenntartva.
* Visszaállítások szükségesek. Ha új műveletként állítja vissza a visszaállítást, a méretezésnek a visszaállításhoz szükséges lemezterületet kell figyelembe helyeznie. A visszaállítás egy azonos méretű megosztásra vagy kötetre történik.
* A váratlan növekedéshez valamilyen puffert kell lefoglalni.

Az előző tényezők alapján a méretezési követelmények a következő egyenlet szerint jeleníthetők meg:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Az alábbi példák azt szemléltetik, hogyan méretezhető a virtuális tömb a követelmények alapján.

#### <a name="example-1"></a>1. példa:
A virtuális tömbön szeretné tudni, hogy

* hozzon létre egy 2 TB-os rétegbeli kötetet vagy megosztást.
* hozzon létre egy 1 TB-os rétegbeli kötetet vagy megosztást.
* helyezzen üzembe egy 300 GB-nyi helyileg rögzített kötetet vagy megosztást.

Az előző kötetek vagy megosztások esetében számítsa ki a helyi szinten a lemezterületre vonatkozó követelményeket.

Először is, az egyes rétegű kötetek/megosztások esetében a helyi foglalás a kötet/megosztás méretének 12%-ával egyenlő. A helyileg rögzített kötet/megosztás esetében a helyi foglalás a helyileg rögzített kötet/megosztás mérete (a kiosztott méreten felül) 10%-a. Ebben a példában a következőkre lesz szüksége:

* 240 – GB helyi foglalás (2 TB-os többrétegű kötet/megosztás esetén)
* 120 – GB helyi foglalás (1 TB-os rétegbeli kötet/megosztás esetén)
* 330 – GB a helyileg rögzített kötethez vagy megosztáshoz (a helyi foglalás 10%-a a 300 GB-os kiosztott mérethez hozzáadva)

Az eddigi helyi szinten szükséges teljes terület a következő: 240 GB + 120 GB + 330 GB = 690 GB.

Másodszor, legalább annyi lemezterületre van szükségünk a helyi szinten, mint a legnagyobb egyszeri foglalás. Ez a további összeg abban az esetben használatos, ha egy Felhőbeli pillanatképből kell visszaállítani. Ebben a példában a legnagyobb helyi foglalás 330 GB (beleértve a fájlrendszer fenntartását is), így azt a 690 GB-ra kell felvenni: 690 GB + 330 GB = 1020 GB.
Ha ezt követően további visszaállításokat hajtottunk végre, az előző visszaállítási műveletből bármikor felszabadítható a terület.

Harmadszor, a helyi Pillanatképek tárolására a teljes helyi tárterület 15%-át, így csak a 85%-a érhető el. Ebben a példában ez körülbelül 1020 GB = 0,85 &ast; kiépített adatlemez TB volt. Tehát a kiépített adatlemez lenne (1020 &ast; (1/0.85)) = 1200 GB = 1,20 TB ~ 1,25 TB (kerekítés a legközelebbi kvartilis)

A nem várt növekedés és az új visszaállítások esetében a 1,25 – 1,5 TB körüli helyi lemezt kell kiépíteni.

> [!NOTE]
> Javasoljuk továbbá, hogy a helyi lemez kiosztása dinamikusan történjen. Ez a javaslat azért van, mert a visszaállítási terület csak akkor szükséges, ha az öt napnál régebbi adatértékeket kívánja visszaállítani. Az elemszintű helyreállítás lehetővé teszi az elmúlt öt nap adatvisszaállítását anélkül, hogy a visszaállításhoz felesleges lemezterületre lenne szükség.


#### <a name="example-2"></a>2. példa
A virtuális tömbön szeretné tudni, hogy

* 2 TB-os rétegbeli kötet kiépítése
* 300 GB-os helyileg rögzített kötet kiépítése

A többplatformos kötetek/megosztások, valamint a helyileg rögzített kötetek/megosztások esetében a helyi terület foglalásának 12%-ában

* 240 – GB helyi foglalás (2 TB-os többrétegű kötet/megosztás esetén)
* 330 – GB a helyileg rögzített kötethez vagy megosztáshoz (a helyi foglalás 10%-a a 300 GB-os kiosztott területhez hozzáadva)

A helyi szinten szükséges teljes terület a következő: 240 GB + 330 GB = 570 GB

A visszaállításhoz szükséges minimális helyi terület 330 GB.

a teljes lemez 15%-a a pillanatképek tárolására szolgál, így csak 0,85 érhető el. Tehát a lemez mérete (900 &ast; (1/0.85)) = 1,06 TB ~ 1,25 TB (kerekítés a legközelebbi kvartilis)

A váratlan növekedéssel a 1,25-1,5 TB-os helyi lemez kiépítését is kiépítheti.

### <a name="group-policy"></a>Csoportházirend
Csoportházirend egy olyan infrastruktúra, amely lehetővé teszi adott konfigurációk megvalósítását a felhasználók és számítógépek számára. Csoportházirend beállításokat Csoportházirend objektumok (GPO-k) tartalmazzák, amelyek a következő Active Directory Domain Services (AD DS) tárolókkal vannak összekapcsolva: helyek, tartományok vagy szervezeti egységek (OU-k). 

Ha a virtuális tömb tartományhoz van csatlakoztatva, akkor a csoportházirend-objektumok alkalmazhatók rá. Ezek a csoportházirend-objektumok olyan alkalmazásokat telepíthetnek, mint például egy víruskereső szoftver, amely negatív hatással lehet a StorSimple virtuális tömb működésére.

Ezért javasoljuk, hogy:

* Győződjön meg arról, hogy a virtuális tömb a Active Directory saját szervezeti egységében (OU) van.
* Győződjön meg arról, hogy a virtuális tömbhöz nincsenek alkalmazva csoportházirend-objektumok (GPO-k). Az öröklés letiltásával biztosíthatja, hogy a virtuális tömb (gyermek csomópont) ne örökölje automatikusan a szülő csoportházirend-objektumait. További információkért keresse fel az [öröklés tiltása](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11))lehetőséget.

### <a name="networking"></a>Hálózatkezelés
A virtuális tömb hálózati konfigurációja a helyi webes felületen keresztül történik. A virtuális hálózati adapter azon a hypervisoron keresztül engedélyezhető, amelyben a virtuális tömb ki van építve. A [hálózati beállítások](storsimple-virtual-array-deploy3-fs-setup.md) lapon konfigurálhatja a virtuális hálózati adapter IP-címét, alhálózatát és átjáróját.  Az eszköz elsődleges és másodlagos DNS-kiszolgálóját, időbeállításait és opcionális proxybeállításait is megadhatja. A hálózati konfiguráció többsége egyszeri beállítás. A virtuális tömb üzembe helyezése előtt tekintse át a [StorSimple hálózati követelményeit](storsimple-ova-system-requirements.md#networking-requirements) .

A virtuális tömb üzembe helyezésekor javasoljuk, hogy kövesse az alábbi ajánlott eljárásokat:

* Győződjön meg arról, hogy az a hálózat, amelyben a virtuális tömb telepítve van, mindig képes 5 Mbps-os internetes sávszélességet (vagy még többet) hozzárendelni.
  
  * Az internetes sávszélességnek a munkaterhelés jellemzőitől és az adatváltozások számától függően eltérőnek kell lennie.
  * A kezelhető adatváltozás közvetlenül arányos az internetes sávszélességgel. Ha például biztonsági mentést készít, az 5 Mbps-os sávszélesség 8 óránként körülbelül 18 GB-os adatváltozást tud fogadni. Négyszer több sávszélesség (20 Mbps) esetén több adatváltozás is kezelhető (72 GB).
* Győződjön meg arról, hogy az internet kapcsolata mindig elérhető. Az eszközök szórványos vagy megbízhatatlan internetkapcsolata a felhőben tárolt adathozzáférés elvesztését eredményezheti, és nem támogatott konfigurációt eredményezhet.
* Ha azt tervezi, hogy az eszközt iSCSI-kiszolgálóként telepíti:
  
  * Javasoljuk, hogy tiltsa le az **IP-cím automatikus beolvasása** lehetőséget (DHCP).
  * Statikus IP-címek konfigurálása. Konfigurálnia kell egy elsődleges és egy másodlagos DNS-kiszolgálót.
  * Ha több hálózati adaptert határoz meg a virtuális tömbben, csak az első hálózati adapter (alapértelmezés szerint ez az adapter **Ethernet**) érheti el a felhőt. A forgalom típusának szabályozásához létrehozhat több virtuális hálózati adaptert a virtuális tömbben (iSCSI-kiszolgálóként konfigurálva), és összekapcsolhatja ezeket a csatolókat a különböző alhálózatokhoz.
* A csak a Felhőbeli sávszélesség (a virtuális tömb által használt) szabályozásához konfigurálja a szabályozást az útválasztón vagy a tűzfalon. Ha a szabályozást a hypervisorban definiálja, az a Felhőbeli sávszélesség helyett az iSCSI-és az SMB-protokollt is szabályozza.
* Győződjön meg arról, hogy a hypervisors időszinkronizálása engedélyezve van. Ha a Hyper-V-t használja, válassza ki a virtuális tömböt a Hyper-V kezelőjében, lépjen a **Beállítások &gt; Integration Services** elemre, és ellenőrizze, hogy az **idő szinkronizálása** be van-e jelölve.

### <a name="storage-accounts"></a>Tárfiókok
A StorSimple virtuális tömb társítható egyetlen Storage-fiókkal is. Ez a Storage-fiók lehet egy automatikusan létrehozott Storage-fiók, egy olyan fiók, amely ugyanabban az előfizetésben van, mint a szolgáltatás, vagy egy másik előfizetéshez kapcsolódó Storage-fiók. További információ: a [virtuális tömb Storage-fiókjainak kezelése](storsimple-virtual-array-manage-storage-accounts.md).

Használja a következő javaslatokat a virtuális tömbhöz társított Storage-fiókokhoz.

* Több virtuális tömb egyetlen Storage-fiókkal való összekapcsolása esetén a virtuális tömb maximális kapacitása (64 TB) és a Storage-fiók maximális mérete (500 TB). Ez korlátozza a Storage-fiókhoz hozzárendelhető teljes méretű virtuális tömbök számát 7-én.
* Új Storage-fiók létrehozásakor
  
  * Azt javasoljuk, hogy a késések csökkentése érdekében a távoli irodában/fiókirodában legközelebb eső régióban hozza létre a StorSimple virtuális tömböt.
  * Vegye figyelembe, hogy a Storage-fiókok nem helyezhetők át különböző régiókban. Emellett a szolgáltatás nem helyezhető át az előfizetések között.
  * Használjon olyan Storage-fiókot, amely redundanciát valósít meg az adatközpontok között. A Geo-Redundant Storage (GRS), a Zone redundáns Storage (ZRS) és a helyileg redundáns tárolás (LRS) egyaránt támogatott a virtuális tömbben való használathoz. A különböző típusú Storage-fiókokkal kapcsolatos további információkért nyissa meg az [Azure Storage-replikációt](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Megosztások és kötetek
A StorSimple virtuális tömbben akkor oszthat ki megosztásokat, ha fájlkiszolgálóként és kötetként van konfigurálva iSCSI-kiszolgálóként. A megosztások és kötetek létrehozásával kapcsolatos ajánlott eljárások a mérethez és a konfigurált típushoz kapcsolódnak.

#### <a name="volumeshare-size"></a>Kötet/megosztás mérete
A virtuális tömbben létrehozhat megosztásokat, ha fájlkiszolgálóként és kötetként van konfigurálva, amikor iSCSI-kiszolgálóként van konfigurálva. A megosztások és kötetek létrehozásával kapcsolatos ajánlott eljárások a mérethez és a konfigurált típushoz kapcsolódnak. 

Vegye figyelembe a következő ajánlott eljárásokat a megosztások vagy kötetek virtuális eszközön való kiosztásakor.

* A többszintes megosztás kiépített méretéhez viszonyított fájlméret hatással lehet a rétegek teljesítményére. A nagyméretű fájlok használata lassú kiugró szintet eredményezhet. Nagyméretű fájlok használata esetén javasoljuk, hogy a legnagyobb fájl mérete kisebb legyen, mint a megosztások méretének 3%-a.
* A virtuális tömbben legfeljebb 16 kötetet/megosztást lehet létrehozni. A helyileg rögzített és a rétegű kötetek/megosztások méretének korlátozásához mindig tekintse meg a [StorSimple virtuális tömbökre vonatkozó korlátozásokat](storsimple-ova-limits.md).
* Kötet létrehozásakor a várt adatfelhasználás és a jövőbeli növekedés tényezője. A kötetet vagy megosztást később nem lehet kibontani.
* A kötet/megosztás létrehozása után a kötet/megosztás mérete nem csökkenthető a StorSimple.
* Ha a StorSimple többszintű kötetre ír, amikor a kötet adatai elérnek egy bizonyos küszöbértéket (a kötet számára fenntartott helyi területhez képest), az i/o szabályozva lesz. A kötetre való írás továbbra is jelentősen lelassítja az IO-t. Bár a kiosztott kapacitáson túlmenően egy többrétegű kötetre is írhat (a felhasználó a kiosztott kapacitáson túl nem áll készen), a rendszer riasztást küld arról, hogy milyen hatással van az előfizetésre. Miután megtalálta a riasztást, fontos, hogy olyan javító mértékeket készítsen, mint például a köteten lévő adat törlése (a kötet bővítése jelenleg nem támogatott).
* A vész-helyreállítási használati esetek esetében, mivel a megengedett megosztások/kötetek száma 16, és a párhuzamosan feldolgozható megosztások/kötetek maximális száma szintén 16, a megosztások/kötetek száma nem rendelkezik a RPO és a RTOs.

#### <a name="volumeshare-type"></a>Kötet/megosztás típusa
A StorSimple a használat alapján két mennyiségi/megosztási típust támogat: a helyileg rögzített és a többszintű. A helyileg rögzített kötetek/megosztások sűrűn vannak kiépítve, míg a lépcsőzetes kötetek/megosztások dinamikusan vannak kiépítve. Egy helyileg rögzített kötet/megosztás nem alakítható át lépcsőzetesen, *vagy fordítva, ha* létrehozták.

Javasoljuk, hogy a StorSimple-kötetek/-megosztások konfigurálásakor alkalmazza az alábbi ajánlott eljárásokat:

* Azonosítsa a kötet típusát a kötet létrehozása előtt telepítendő munkaterhelések alapján. Helyileg rögzített köteteket használhat olyan számítási feladatokhoz, amelyek helyi adatgaranciákat igényelnek (még a felhő meghibásodása során is), és amelyek alacsony Felhőbeli késést igényelnek. Miután létrehozta a kötetet a virtuális tömbben, a kötet típusa nem módosítható a helyileg rögzítettről, illetve *fordítva*. Tegyük fel például, hogy helyileg rögzített köteteket hoz létre a virtuális gépeket üzemeltető SQL-munkaterhelések vagy munkaterhelések telepítésekor. a fájlmegosztási számítási feladatokhoz használjon többplatformos köteteket.


#### <a name="volume-format"></a>Kötet formátuma
Miután létrehozta a StorSimple-köteteket az iSCSI-kiszolgálón, inicializálnia, csatlakoztatnia és formázni kell a köteteket. Ez a művelet a StorSimple-eszközhöz csatlakoztatott gazdagépen történik. A következő ajánlott eljárások használata javasolt a kötetek StorSimple-gazdagépen való csatlakoztatásához és formázásához.

* Hajtson végre egy gyors formátumot az összes StorSimple-köteten.
* Egy StorSimple-kötet formázásakor használjon 64 KB-os foglalási egység méretét (az alapértelmezett érték 4 KB). Az 64 KB-os au-t az általános StorSimple-számítási feladatokhoz és egyéb számítási feladatokhoz a házon belül végzett tesztelésen alapul.
* Ha iSCSI-kiszolgálóként konfigurált StorSimple virtuális tömböt használ, ne használjon átfedő köteteket vagy dinamikus lemezeket, mivel a StorSimple nem támogatja ezeket a köteteket vagy lemezeket.

#### <a name="share-access"></a>Megosztási hozzáférés
Ha megosztásokat hoz létre a virtuális tömb fájlkiszolgálón, kövesse az alábbi irányelveket:

* Megosztás létrehozásakor egyetlen felhasználó helyett rendeljen hozzá egy felhasználói csoportot megosztási rendszergazdaként.
* A megosztás létrehozása után az NTFS-engedélyeket a Windows Intézőn keresztül a megosztások szerkesztésével kezelheti.

#### <a name="volume-access"></a>Mennyiségi hozzáférés
Ha az iSCSI-köteteket a StorSimple virtuális tömbön konfigurálja, fontos, hogy szükség esetén szabályozza a hozzáférést. Annak megállapításához, hogy mely gazdagépek férhetnek hozzá a kötetekhez, hozzon létre és társítson hozzáférés-vezérlési rekordokat (ACR-EK) StorSimple-kötetekkel.

A StorSimple-kötetek ACR-EK konfigurálásakor használja az alábbi ajánlott eljárásokat:

* Mindig rendeljen hozzá legalább egy ACR-t egy kötethez.

* Ha egynél több ACR-t rendel egy kötethez, győződjön meg arról, hogy a kötet nem érhető el oly módon, hogy egyszerre több nem fürtözött gazdagép is hozzáférhessen. Ha több ACR-EK is hozzárendelt egy kötethez, egy figyelmeztető üzenet jelenik meg, amely áttekinti a konfigurációt.

### <a name="data-security-and-encryption"></a>Adatbiztonság és titkosítás
A StorSimple virtuális tömbje adatbiztonsági és titkosítási funkciókkal rendelkezik, amelyek biztosítják az adatok titkosságát és integritását. Ezeknek a szolgáltatásoknak a használatakor javasoljuk, hogy kövesse az alábbi gyakorlati tanácsokat: 

* Definiáljon egy felhőalapú tárolási titkosítási kulcsot az AES-256 titkosítás létrehozásához, mielőtt az adatok elküldése a virtuális tömbről a felhőbe történik. Erre a kulcsra nincs szükség, ha az adatai titkosítva kezdődnek. A kulcs a kulcs-felügyeleti rendszer, például az [Azure Key Vault](../key-vault/general/overview.md)használatával hozható létre és biztonságos.
* Ha a Storage-fiókot a StorSimple Manager szolgáltatáson keresztül konfigurálja, akkor győződjön meg arról, hogy a TLS-mód lehetővé teszi, hogy biztonságos csatornát hozzon létre a StorSimple-eszköz és a felhő közötti hálózati kommunikációhoz.
* A Storage-fiókokhoz tartozó kulcsok (az Azure Storage szolgáltatáshoz való hozzáférés) rendszeres időközönkénti létrehozása, hogy a rendszer a rendszergazdák módosított listáján alapuló hozzáférési módosításokat is figyelembe veszi.
* Az Azure-ba való elküldés előtt a virtuális tömbön lévő adatai tömörítettek és deduplikálva lesznek. A Windows Server-gazdagépen nem ajánlott az adat-visszamásolási szerepkör-szolgáltatás használata.

## <a name="operational-best-practices"></a>Ajánlott üzemeltetési eljárások
Az operatív ajánlott eljárások olyan irányelvek, amelyeket követni kell a virtuális tömb napi felügyelete vagy működése során. Ezek a gyakorlatok olyan felügyeleti feladatokra vonatkoznak, mint például a biztonsági másolatok készítése, a biztonságimásolat-készlet visszaállítása, a feladatátvétel végrehajtása, a tömb inaktiválása és törlése, a rendszer használatának figyelése és állapota, valamint a Víruskeresés futtatása a virtuális tömbön.

### <a name="backups"></a>Biztonsági másolatok
A virtuális tömb adatai a felhőbe való mentés két módon, a teljes eszköz alapértelmezett automatikus biztonsági mentése a 22:30-as vagy az igény szerinti manuális biztonsági mentés használatával történik. Alapértelmezés szerint az eszköz automatikusan hozza létre az összes tárolt adattal kapcsolatos napi Felhőbeli pillanatképet. További információkért tekintse meg a [StorSimple virtuális tömb biztonsági mentését](storsimple-virtual-array-backup.md)ismertető témakört.

Az alapértelmezett biztonsági másolatokhoz társított gyakoriság és adatmegőrzés nem módosítható, de beállíthatja, hogy a napi biztonsági másolatok Mikor legyenek elindítva naponta. Az automatikus biztonsági mentések kezdő időpontjának konfigurálásakor javasoljuk, hogy:

* A biztonsági mentések ütemezése a munkaidőn kívülre. A biztonsági mentés kezdési időpontja nem eshet el számos gazda IO-val.
* Manuális, igény szerinti biztonsági mentést kezdeményezhet az eszköz feladatátvételének megtervezése vagy a karbantartási időszak előtt a virtuális tömbben lévő adatok védelme érdekében.

### <a name="restore"></a>Visszaállítás
A következő két módon állítható vissza egy biztonságimásolat-készletből: visszaállítás másik kötetre vagy megosztás vagy egy elemszintű helyreállítás végrehajtása (csak a fájlkiszolgálón konfigurált virtuális tömbben érhető el). Az elemszintű helyreállítás lehetővé teszi a fájlok és mappák részletes helyreállítását a StorSimple-eszközön lévő összes megosztás Felhőbeli biztonsági másolatából. További információért lépjen a [visszaállítás biztonsági másolatból](storsimple-virtual-array-clone.md)elemre.

Visszaállítás esetén tartsa szem előtt a következő irányelveket:

* A StorSimple virtuális tömb nem támogatja a helyben történő visszaállítást. Ez egy kétlépéses folyamat által könnyen megvalósítható: helyet szabadíthat fel a virtuális tömbben, majd visszaállíthat egy másik kötetet/megosztást.
* Helyi kötet visszaállításakor ne feledje, hogy a visszaállítás hosszú ideig futó művelet lesz. Bár a kötet gyorsan online állapotba kerülhet, az adatai továbbra is hidratálva lesznek a háttérben.
* A kötet típusa változatlan marad a visszaállítási folyamat során. A rétegű kötetek egy másik, lépcsőzetesen rögzített kötetre, valamint egy helyileg rögzített kötetre állnak vissza egy másik helyileg rögzített kötetre.
* Ha egy kötetet vagy egy megosztást egy biztonságimásolat-készletből próbál visszaállítani, ha a visszaállítási feladatot nem sikerül végrehajtani, akkor a cél kötet vagy megosztás továbbra is létrehozható a portálon. Fontos, hogy törölje ezt a nem használt célként megadott kötetet vagy megosztást a portálon, hogy csökkentse az ebből az elemből fakadó jövőbeli problémákat.

### <a name="failover-and-disaster-recovery"></a>Feladatátvétel és vész-helyreállítás
Az eszközök feladatátvétele lehetővé teszi az adatok áttelepítését az adatközpontban lévő *forrásoldali* eszközről egy másik, ugyanazon vagy *egy másik földrajzi* helyen található eszközre. Az eszköz feladatátvétele a teljes eszközre irányul. A feladatátvétel során a forrás eszköz Felhőbeli adatai megváltoztatják a célként megadott eszköz tulajdonjogát.

A StorSimple virtuális tömbök esetében csak egy StorSimple Manager szolgáltatás által felügyelt virtuális tömbre lehet átvenni a feladatátvételt. Egy 8000 sorozatú eszközre vagy egy másik StorSimple Manager szolgáltatás által kezelt tömbre (mint a forrásoldali eszközre) való feladatátvétel nem engedélyezett. Ha többet szeretne megtudni a feladatátvételi szempontokról, ugorjon [az eszköz feladatátvételének előfeltételei](storsimple-virtual-array-failover-dr.md)című témakörre.

Ha feladatátvételt hajt végre a virtuális tömbön, tartsa szem előtt a következőket:

* Tervezett feladatátvétel esetén ajánlott az összes kötetet/megosztást offline állapotba helyezni a feladatátvétel megkezdése előtt. Kövesse az operációs rendszerre vonatkozó útmutatást, hogy a kötetek/megosztások offline állapotba kerüljön a gazdagépen, majd a virtuális eszközön végezze el az offline állapotot.
* A fájlkiszolgáló vész-helyreállítási (DR) esetén azt javasoljuk, hogy a cél eszközt ugyanahhoz a tartományhoz csatlakoztassa, mint a forrást, hogy a megosztási engedélyek automatikusan fel legyenek oldva. Ebben a kiadásban csak az azonos tartományban lévő céleszköz feladatátvétele támogatott.
* A DR sikeres befejeződése után a rendszer automatikusan törli a forrás eszközt. Bár az eszköz már nem érhető el, a gazdagép rendszeren kiépített virtuális gép továbbra is erőforrásokat használ. Javasoljuk, hogy törölje a virtuális gépet a gazdagépről, hogy elkerülje a felmerülő díjakat.
* Ne feledje, hogy még akkor is, ha a feladatátvétel sikertelen, **az adatai mindig biztonságosak a felhőben**. Vegye figyelembe a következő három forgatókönyvet, amelyekben a feladatátvétel nem fejeződött be sikeresen:
  
  * Hiba történt a feladatátvétel kezdeti szakaszaiban, például a DR előzetes ellenőrzések végrehajtásakor. Ebben az esetben a cél eszköz továbbra is használható. Próbálja megismételni a feladatátvételt ugyanazon a céleszköz-eszközön.
  * Hiba történt a tényleges feladatátvételi folyamat során. Ebben az esetben a megcélzott eszköz használhatatlanként van megjelölve. Meg kell adnia és konfigurálnia kell egy másik cél virtuális tömböt, és ezt a feladatátvételhez kell használnia.
  * A feladatátvétel a forrásoldali eszköz törlését követően fejeződött be, de a céleszköz problémákba ütközik, és nem fér hozzá semmilyen adattal. Az adatai továbbra is biztonságosak a felhőben, és egyszerűen lekérhető egy másik virtuális tömb létrehozásával, majd a DR. cél eszközként való használatával.

### <a name="deactivate"></a>Inaktiválás
Ha inaktiválja a StorSimple virtuális tömböt, megszakítja a kapcsolatot az eszköz és a hozzá tartozó StorSimple Manager szolgáltatás között. Az Inaktiválás **állandó** művelet, ezért nem vonható vissza. Inaktivált eszköz nem regisztrálható újra a StorSimple Manager szolgáltatásban. További információ: [inaktiválja és törölje a StorSimple virtuális tömböt](storsimple-virtual-array-deactivate-and-delete-device.md).

A virtuális tömb inaktiválásakor tartsa szem előtt az alábbi ajánlott eljárásokat:

* A virtuális eszköz inaktiválása előtt készítsen egy Felhőbeli pillanatképet az összes adattal. Ha inaktiválja a virtuális tömböt, a rendszer az összes helyi eszközre vonatkozó adatvesztést elveszi. A Felhőbeli Pillanatképek elkészítése lehetővé teszi, hogy egy későbbi időpontban helyreállítsa az adatokat.
* A StorSimple virtuális tömb inaktiválása előtt győződjön meg arról, hogy leállítja vagy törli az adott eszköztől függő ügyfeleket és gazdagépeket.
* Inaktivált eszköz törlése, ha már nem használja, így nem számít fel díjat.

### <a name="monitoring"></a>Figyelés
Annak biztosítása érdekében, hogy a StorSimple virtuális tömbje folyamatos kifogástalan állapotú legyen, figyelnie kell a tömböt, és biztosítania kell, hogy a rendszer információkat kapjon a riasztásokról. A virtuális tömb általános állapotának monitorozásához hajtsa végre az alábbi ajánlott eljárásokat:

* A figyelés konfigurálásával követheti nyomon a virtuális tömb adatlemezének és az operációsrendszer-lemeznek a használatát. Ha a Hyper-V-t futtatja, a virtualizációs gazdagépek figyeléséhez System Center Virtual Machine Manager (SCVMM) és System Center Operations Manager kombinációját használhatja.
* Az e-mail-értesítések konfigurálása a virtuális tömbön a riasztások bizonyos használati szinteken való elküldéséhez.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Keresési és víruskeresési alkalmazások indexelése
A StorSimple virtuális tömb automatikusan képes a helyi szinten lévő adatok a Microsoft Azure felhőbe való begyűjtésére. Ha egy alkalmazás, például egy indexelési keresés vagy egy víruskeresés a StorSimple tárolt információ vizsgálatára szolgál, ügyelnie kell arra, hogy a felhőalapú adathozzáférés ne legyen elérhető, és vissza legyen húzva a helyi szinten.

Javasoljuk, hogy az indexelési keresés vagy a víruskeresés konfigurálásakor a következő ajánlott eljárásokat alkalmazza a virtuális tömbön:

* Tiltsa le az automatikusan konfigurált teljes ellenőrzési műveleteket.
* A lépcsőzetes kötetek esetében állítsa be az index Search vagy a Virus Scan alkalmazást a növekményes vizsgálat végrehajtásához. Ez csak azokat az új, valószínűleg a helyi szinten tartózkodó adatszinteket vizsgálja meg. A felhőbe felépülő, növekményes művelet során a rendszer nem fér hozzá.
* Győződjön meg arról, hogy a megfelelő keresési szűrők és beállítások úgy vannak konfigurálva, hogy csak a kívánt típusú fájlok legyenek beolvasva. A képfájlok (JPEG, GIF és TIFF) és a mérnöki rajzok például nem ellenőrizhetők a növekményes vagy a teljes index újraépítése során.

Ha Windows indexelési folyamatot használ, kövesse az alábbi irányelveket:

* A többplatformos kötetek esetében ne használja a Windows indexelő, mert a nagy mennyiségű adatmennyiséget (TBs) idéz elő a felhőből, ha az indexet gyakran kell újraépíteni. Az index újraépítése az összes fájltípust lekéri a tartalom indexeléséhez.
* A Windows indexelési folyamatát a helyileg rögzített köteteknél használja, mivel ez csak a helyi rétegek adatelérését fogja elérni az index létrehozásához (a Felhőbeli adatforgalom nem lesz elérhető).

### <a name="byte-range-locking"></a>Byte-tartomány zárolása
Az alkalmazások a fájlokon belül zárolják a megadott bájtok egy tartományát. Ha a StorSimple írt alkalmazások esetében engedélyezve van a bájt-tartomány zárolása, akkor a rétegek nem működnek a virtuális tömbben. A rétegek működéséhez az elért fájlok összes területét fel kell oldani. A bájtok tartományának zárolása nem támogatott a virtuális tömbben lévő rétegű kötetek esetében.

A byte Range zárolásának enyhítésére javasolt intézkedések a következők:

* Kapcsolja ki az alkalmazás logikájában a kikapcsolási tartomány zárolását.
* Az alkalmazáshoz társított adatmennyiségek esetében használjon helyileg rögzített köteteket (a rétegek helyett). A helyileg rögzített kötetek nem a felhőbe kerülnek.
* Ha olyan helyileg rögzített köteteket használ, amelyeken engedélyezve van a byte Range, a kötet a visszaállítás befejeződése előtt online állapotba kerülhet. Ezekben a példányokban meg kell várnia, amíg a visszaállítás be nem fejeződik.

## <a name="multiple-arrays"></a>Több tömb
Előfordulhat, hogy több virtuális tömböt kell központilag telepíteni ahhoz, hogy az egyre növekvő munkakészletet lehessen felvenni a felhőbe, ami hatással lehet az eszköz teljesítményére. Ezekben a példányokban a legjobb megoldás az eszközök méretezése a munkakészlet növekedésével. Ehhez szükség van egy vagy több eszköz hozzáadására a helyszíni adatközpontban. Az eszközök hozzáadásakor a következőket teheti:

* Az aktuális adathalmaz felosztása.
* Új munkaterhelések üzembe helyezése új eszköz (ek) ra.
* Több virtuális tömb üzembe helyezése esetén javasolt a terheléselosztási perspektívából a tömböt a különböző hypervisor-gazdagépekre terjeszteni.
* Több virtuális tömb (ha fájlkiszolgáló vagy iSCSI-kiszolgálóként van konfigurálva) elosztott fájlrendszer névtérben is üzembe helyezhető. A részletes lépésekért látogasson el a [hibrid felhőalapú tárolás telepítési útmutatóját a elosztott fájlrendszer névtér megoldásához](https://www.microsoft.com/download/details.aspx?id=45507). Elosztott fájlrendszer replikáció használata jelenleg nem ajánlott a virtuális tömbben való használathoz. 

## <a name="see-also"></a>Lásd még
Ismerje meg, hogyan [felügyelheti a StorSimple virtuális tömböt](storsimple-virtual-array-manager-service-administration.md) a StorSimple Manager szolgáltatáson keresztül.