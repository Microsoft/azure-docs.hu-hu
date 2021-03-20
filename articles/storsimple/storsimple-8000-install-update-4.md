---
title: A 4-es frissítés telepítése a StorSimple 8000 Series eszközön | Microsoft Docs
description: A cikk azt ismerteti, hogyan telepíthető a StorSimple 8000 Series Update 4 a StorSimple 8000 Series eszközre.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: ed6f9d58c5c54c88acf8e3a0e7fda7d2d65b8637
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514325"
---
# <a name="install-update-4-on-your-storsimple-device"></a>A 4-es frissítés telepítése a StorSimple-eszközön

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan telepítheti a 4. frissítést egy korábbi verziót futtató StorSimple-eszközön a Azure Portal és a gyorsjavítási módszer használatával. A gyorsjavítási módszert akkor kell használni, ha egy átjáró a StorSimple-eszköztől eltérő hálózati adapteren van konfigurálva, és az 1. frissítés előtti verzióról próbál frissíteni.

A 4. frissítés magában foglalja az eszköz szoftverét, az USM belső vezérlőprogram, az LSI-illesztőprogram és a belső vezérlőprogram, a Storport és a ûrkikötõt, az operációs rendszer biztonsági frissítéseit, valamint az operációs rendszer más  Az eszköz szoftvere, az USM belső vezérlőprogram, a ûrkikötõt, a Storport és más operációsrendszer-frissítések nem zavaró frissítések. A nem zavaró vagy rendszeres frissítések a Azure Portal vagy a gyorsjavítási módszer használatával alkalmazhatók. A lemez belső vezérlőprogram-frissítései zavaró frissítések, és csak az eszköz Windows PowerShell felületén keresztül alkalmazhatók a gyorsjavítási módszer használatával.

> [!IMPORTANT]
> * A telepítés előtt manuális és automatikus ellenőrzéseket végeznek az eszközök állapotának meghatározásához a hardver állapota és a hálózati kapcsolat alapján. Ezeket az előzetes ellenőrzéseket csak akkor hajtja végre, ha a Azure Portalről alkalmazza a frissítéseket.
> * Javasoljuk, hogy telepítse a szoftvert és az egyéb rendszeres frissítéseket a Azure Portal használatával. Az eszköz Windows PowerShell-felületén (a frissítések telepítéséhez) csak akkor kell megjelennie, ha a portálon nem sikerül a frissítés előtti átjáró-ellenőrzés. Attól függően, hogy melyik verziót szeretné frissíteni, a frissítések telepítése 4 órát is igénybe vehet (vagy nagyobb). A karbantartási mód frissítéseit az eszköz Windows PowerShell-felületén keresztül is telepíteni kell. Mivel a karbantartási mód frissítései nem zavaró frissítések, ezért a rendszer leállítja az eszközét.
> * Ha a választható StorSimple Snapshot Manager futtatja, győződjön meg arról, hogy az eszköz frissítése előtt frissítette a Snapshot Manager verzióját a 4-es frissítésre.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>A 4-es frissítés telepítése a Azure Portal használatával
Az alábbi lépések végrehajtásával frissítheti az eszközt a [4-es frissítéssel](storsimple-update4-release-notes.md).

> [!NOTE]
> A Microsoft további diagnosztikai adatokat kér le az eszközről. Ennek eredményeképpen, amikor az operatív csapatunk olyan eszközöket azonosít, amelyekkel problémák léptek fel, jobban felkészültünk az adatok gyűjtésére az eszközről és a problémák diagnosztizálására. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Ellenőrizze, hogy az eszközön fut-e a **StorSimple 8000 Series Update 4 (6.3.9600.17820)**. A **legutóbbi frissítés dátumát** is módosítani kell.

* Ekkor láthatja, hogy a karbantartási mód frissítései elérhetők (ez az üzenet akár 24 óráig is megjelenhet a frissítések telepítése után). A karbantartási mód frissítései olyan zavaró frissítések, amelyek az eszköz leállását eredményezik, és csak az eszköz Windows PowerShell-felületén keresztül alkalmazhatók.

* Töltse le a karbantartási mód frissítéseit a következő témakörben ismertetett módon: a [gyorsjavítások letöltése](#to-download-hotfixes) a KB4011837 kereséséhez és letöltéséhez, amely a lemez belső vezérlőprogram-frissítéseit telepíti (a többi frissítést már most már telepíteni kell). A karbantartási mód frissítéseinek telepítéséhez kövesse a [karbantartási módú gyorsjavítások telepítése és ellenőrzése](#to-install-and-verify-maintenance-mode-hotfixes) című témakörben ismertetett lépéseket.

## <a name="install-update-4-as-a-hotfix"></a>A 4-es frissítés telepítése gyorsjavításként
A 4. frissítés telepítéséhez javasolt módszer a Azure Portalon keresztül.

Akkor használja ezt az eljárást, ha nem sikerül az átjáró ellenőrzése, amikor a Azure Portalon keresztül kísérli meg telepíteni a frissítéseket. Az ellenőrzés sikertelen, mert az átjáró nem adat 0 hálózati adapterhez van rendelve, és az eszközön az 1. frissítés előtt egy szoftververzió fut.

A gyorsjavítási módszer használatával frissíthető szoftverek a következők:

* Frissítés 0,1, 0,2, 0,3
* 1. frissítés, 1,1, 1,2
* 2. frissítés, 2,1, 2,2
* 3. frissítés, 3,1


A gyorsjavítási módszer a következő három lépést foglalja magában:

1. Töltse le a gyorsjavításokat a Microsoft Update-katalógusból.
2. Telepítse és ellenőrizze a normál módú gyorsjavításokat.
3. Telepítse és ellenőrizze a karbantartási mód gyorsjavítását.

#### <a name="download-updates-for-your-device"></a>Az eszköz frissítéseinek letöltése

A következő gyorsjavításokat le kell töltenie és telepítenie kell az előírt sorrendben és a javasolt mappákban:

| Sorrend | KB | Description | Frissítéstípus | Telepítés időpontja |Telepítés mappába|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Szoftverfrissítés |Szabályos <br></br>Nem zavaró |~ 25 perc |FirstOrderUpdate|
| 2a. |KB4011841 <br> KB4011842 |LSI-illesztőprogram és belső vezérlőprogram frissítései <br> USM belső vezérlőprogram frissítése (3,38-es verzió) |Szabályos <br></br>Nem zavaró |~ 3 óra <br> (tartalmazza a következőt: 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2b. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Operációs rendszer biztonsági frissítései csomag <br> A Windows Server 2012 R2 letöltése |Szabályos <br></br>Nem zavaró |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Operációs rendszer frissítési csomagja <br> A Windows Server 2012 R2 letöltése |Szabályos <br></br>Nem zavaró |- |SecondOrderUpdate|

Előfordulhat, hogy a lemez belső vezérlőprogram-frissítéseit is telepítenie kell az előző táblázatokban látható összes frissítésre. A parancsmag futtatásával ellenőrizheti, hogy szüksége van-e a lemez belső vezérlőprogram-frissítéseire `Get-HcsFirmwareVersion` . Ha a következő belső vezérlőprogram-verziókat futtatja:,,,,,, `XMGJ` `XGEG` `KZ50` `F6C2` `VR08` `N002` `0106` akkor nem kell telepítenie ezeket a frissítéseket.

| Sorrend | KB | Description | Frissítéstípus | Telepítés időpontja | Telepítés mappába|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Lemez belső vezérlőprogramja |Karbantartás <br></br>Zavaró |~ 30 perc | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Ezt az eljárást csak egyszer kell végrehajtani a 4. frissítés alkalmazásához. A további frissítések alkalmazásához használhatja a Azure Portal.
> * Ha a 3. vagy a 3,1-es frissítésből frissíti a frissítést, a teljes telepítési idő 4 óra.
> * Mielőtt alkalmazza ezt az eljárást a frissítés alkalmazásához, győződjön meg arról, hogy mind az Eszközkezelő online, mind a hardver-összetevők kifogástalan állapotban vannak.

A gyorsjavítások letöltéséhez és telepítéséhez hajtsa végre a következő lépéseket.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Következő lépések
További információ a [4. frissítés kiadásáról](storsimple-update4-release-notes.md).

