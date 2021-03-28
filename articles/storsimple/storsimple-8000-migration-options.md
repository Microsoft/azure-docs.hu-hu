---
title: Adatáttelepítési lehetőségek a StorSimple 5000-7000 Series-eszközökről
description: Áttekintést nyújt az adatok StorSimple 5000-7000 sorozatból való átadásának lehetőségeiről.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: cc67b678fabeb2a7878490a4ea713acb5709cdbc
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643789"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Adatmigrálási lehetőségek a StorSimple 5000-7000-es sorozatból 

> [!IMPORTANT]
> 2019. július 9-én a StorSimple 5000/7000-sorozat eléri a támogatási (EOS) állapot végét. Javasoljuk, hogy a StorSimple 5000/7000 Series-ügyfelek a dokumentumban ismertetett alternatívák egyikére váltsanak át.

A StorSimple 5000-7000-es sorozat [támogatásának vége a](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) 2019. júliusában érhető el. A StorSimple 5000-7000 sorozatú ügyfeleknek lehetősége van arra, hogy más Azure-beli hibrid szolgáltatásokra frissítsen. Ez a cikk az adatáttelepítés során elérhető Azure Hybrid-beállításokat ismerteti. 

## <a name="migration-options"></a>Migrálási lehetőségek

Az StorSimple 5000-7000 sorozatú ügyfelek az Azure-ban vagy a külső felektől származó lehetőségeket is igénybe vehetik.

### <a name="azure-options"></a>Azure-lehetőségek

#### <a name="upgrade-to-storsimple-8000-series"></a>Frissítés a StorSimple 8000 sorozatra

Frissítsen a StorSimple 8000-es sorozatra, és folytassa a StorSimple platformon.  Ez a frissítési útvonal megköveteli, hogy az ügyfelek az 5000-7000-es sorozatú eszközöket egy 8000-sorozatra cseréljenak. Az adatok áttelepítése az 5000-7000 Series eszközről az áttelepítési eszköz használatával történik. Az áttelepítés sikeres befejezését követően a StorSimple 8000 Series-eszközök továbbra is az Azure Blob Storageba kerülnek. 

Az adatok StorSimple 8000 sorozat használatával történő áttelepítésével kapcsolatos további információkért nyissa meg az [adatok áttelepítését a StorSimple 5000-7000 sorozatból az 8000 Series eszközre](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migrálás az Azure File Syncre

Ez a vadonatúj áttelepítési lehetőség lehetővé teszi, hogy az ügyfelek a szervezet fájlmegosztást a Azure Files tárolják. Ezeket a fájlokat a rendszer az Azure File Sync (AFS) használatával központosítja a helyszíni hozzáféréshez. Az AFS telepíthető Windows Server-gazdagépre. A tényleges adatáttelepítés ezután a gazdagép másolata vagy az áttelepítési eszköz használatával történik.

Az adatok Azure File Syncba való áttelepítésével kapcsolatos további információkért lépjen az [adatok áttelepítéséhez a StorSimple 5000-7000 sorozatból a Azure file Syncra](../storage/files/storage-files-migration-storsimple-8000.md).

### <a name="third-party-options"></a>Harmadik féltől származó beállítások

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrálás a Panzura Freedom NAS-ra

A StorSimple 5000-7000-ügyfelek áttelepíthetik a Panzura Freedom NAS-ra az Azure-ban tárolt adatuk megőrzéséhez. A Panzura Freedom megoldás olyan NAS-megoldást biztosít, amely az adatközpontokat, az irodákat, a nyilvános és a magánjellegű felhőket fedi le. A megoldás lehetővé teszi a helyi, hibrid és Felhőbeli munkafolyamatokat az NFS-, SMB-és mobil ügyfelek számára. 

Ezt az áttelepítést a Panzura és az ügyfelek is támogatják, ha a [Panzura webhely](https://panzura.com/storsimple-migration/)áttelepítési támogatását kéri.

#### <a name="migrate-to-cohesity"></a>Migrálás az összetartó rendszerbe

Az összetartó funkció lehetővé teszi, hogy az adatok áttelepíthetők legyenek a jelenlegi StorSimple 5000 – 7000-ről az Azure-ban zajló összetartó adatplatformra. A többtényezős adatplatform egy szoftveresen definiált webes megoldás, amely egyetlen Felhőbeli natív megoldásba vonja össze a fájlokat, a biztonsági másolatokat, az objektumokat és a virtuális gépeket. Az adatplatformba való Migrálás után az adatok és alkalmazások a felhőből a Core-ra, egyetlen üvegtábla használatával kezelhetők, védhetők és helyezhetők üzembe. Az egységesség mellett a három csomópontot is megkezdheti. 

További információ [az áttelepítésről az összetartó Adatplatformra](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Migrálás a Nasuni

A Nasuni megkönnyíti a StorSimple 5000-7000-ügyfelek számára az Azure-ban való Migrálás és az azokhoz való hozzáférés megtartását.  A Nasuni egy vezető Azure-alapú NAS-tárolási megoldás, amely lehetővé teszi, hogy az ügyfelek a helyszíni megoldásoktól, a felhő gazdaságosságával és a skálázással kapcsolatos teljesítményre és biztonságra számíthatnak.  A nagy teljesítményű fájlok tárolásán, a Nasuni-ben és az Azure-ban a Backup és a DR szolgáltatáson túl, miközben lehetővé teszi az adatok megosztását és kezelését a világ különböző pontjain a központosított file Storage szolgáltatással. 

A Nasuni megkönnyíti a Migrálás megkönnyítését – kezdjen hozzá még ma: https://www.nasuni.com/blog-migrating-off-storsimple/

#### <a name="migrate-to-talon-fast"></a>Migrálás a talonba gyors

A Talon megkönnyíti a StorSimple 5000-7000 ügyfelei számára, hogy továbbra is kihasználják a StorSimple-platformon alapuló előnyöket (a korlátlan Felhőbeli erőforrások által támogatott kis helyszíni lábnyomot) még nagyobb funkciókkal.  A Talon gyors megoldásával az ügyfelek áttelepíthetik és megtarthatják az Azure-ban tárolt adatmennyiséget, miközben mostantól még a csak kis méretű szoftvereket is megtekinthetik, így például a globális fájlok zárolását, a globális névteret és a többhelyes együttműködést.  A Talon egy vezető Azure ökoszisztéma-megoldás, amely a globális ügyfelekkel együttműködve áttelepíti a helyszíni fájlkiszolgáló munkaterheléseit egy konszolidált, Azure-alapú lábnyomba anélkül, hogy a felhasználói munkafolyamatot vagy élményt veszélyeztetné.  

További információ a felhőalapú konszolidált vállalatokra való fejlődésről.

## <a name="migration---frequently-asked-questions"></a>Migrálás – gyakori kérdések

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>K. Mikor éri el a StorSimple 5000 és az 7000 sorozatú eszközt a szolgáltatás végén? 

A. A StorSimple 5000-7000-es sorozat [a szolgáltatás végét](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) éri el a 2019. júliusában. A szolgáltatás vége azt jelenti, hogy a Microsoft nem fogja tudni támogatni az eszközök hardveres és szoftveres támogatását az 2019. július után. Nyomatékosan javasoljuk, hogy kezdjen el egy tervet készíteni az adatok az eszközökről való átimportálásához.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>K. Mi történik az Azure-ban tárolt adattal?  

A. Ha egy újabb szolgáltatásba telepíti át, továbbra is használhatja az Azure-ban tárolt adatátvitelt. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>K. Mi történik az StorSimple-eszközön helyileg tárolt adataival? 

A. A helyi eszközön lévő információk átmásolhatók az újabb szolgáltatásba az áttelepítési dokumentumokban leírtak szerint.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>K. Mi történik, ha szeretném megőrizni a StorSimple 5000/7000 Series készüléket? 

A. Amíg a szolgáltatások továbbra is működhetnek, a Microsoft többé nem fog tudni hardveres és szoftveres támogatást biztosítani. Az üzletmenet folytonossága érdekében erősen ajánlott az áttelepítés.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>K. Milyen lehetőségek állnak rendelkezésre az adatok áttelepíthetők a StorSimple 5000-7000 Series-eszközökről? 

A. A forgatókönyvtől függően a StorSimple 5000-7000 Series felhasználói a következő áttelepítési lehetőségek közül választhatnak. 

 - **Frissítés a 8000 sorozatra**: akkor használja ezt a beállítást, ha folytatni szeretné a StorSimple platformot. 
 - **Áttelepítés Azure file Syncre**: ezt a beállítást akkor használja, ha az Azure natív formátumára szeretne váltani. A fájlmegosztás központi felügyeletéhez Azure File Sync is használhatja. 

Az itt felsorolt áttelepítési lehetőségek megvitatásához lépjen kapcsolatba Microsoft ügyfélszolgálata.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>K. Támogatott-e az áttelepítés más tárolási megoldásokra?

A. Igen. A rendszer a más tárolási megoldásokra való áttelepítést is támogatja az adatfogadó példány használatával.

### <a name="q-is-migration-supported-by-microsoft"></a>K. Támogatja a Microsoft a Migrálás szolgáltatást? 

A. A 5000-es vagy a 7000-es sorozatból való Migrálás teljes mértékben támogatott művelet. A Microsoft a Migrálás megkezdése előtt javasolja a támogatás elérését. Az áttelepítés jelenleg egy támogatott művelet. Ha a StorSimple 5000-7000 Series-eszközről kívánja áttelepíteni az adatait, [Nyisson meg egy támogatási jegyet](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>K. Mi a díjszabási modell az áttelepítési lehetőségekhez?

A. A Migrálás díja a választott lehetőségtől függően változhat. Az áttelepítés maga után ingyenes, ha úgy dönt, hogy StorSimple 8000-es sorozatra frissít, akkor a hardvereszköz ára lesz. 

Hasonlóképpen, a Azure File Sync használatakor a szolgáltatás előfizetési díja is érvényes. Minden esetben az ügyfeleknek a folyamatos tárolási költségeket is fizetniük kell. A becslések a következőkre vonatkoznak: 
- [StorSimple díjszabása](https://azure.microsoft.com/pricing/details/storsimple/)  
- [Az AFS díjszabása]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>K.  Mennyi ideig tart az áttelepítés befejezése?

A. Az adatok áttelepítésének ideje az adatok mennyiségétől és a kiválasztott verziófrissítési lehetőségtől függ. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>K. Mi a támogatási dátum vége a StorSimple 8000 sorozathoz?

A. A StorSimple 8000 sorozat támogatási dátumának vége [itt](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)jelenik meg.


## <a name="next-steps"></a>Következő lépések
 - [Adatok migrálása StorSimple 5000-7000-sorozatból egy 8000 sorozatú eszközre](storsimple-8000-migrate-from-5000-7000.md).
 - [Adatok migrálása StorSimple 5000-7000-sorozatból Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md)
