---
title: Telepítse az 5. frissítést a StorSimple 8000 Series eszközön | Microsoft Docs
description: A cikk azt ismerteti, hogyan telepítheti a StorSimple 8000 Series Update 5-öt a StorSimple 8000 Series eszközön.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: bbac6eade634ffcfdc47ae3d22b32e0bd429b7c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85513174"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Telepítse az 5. frissítést a StorSimple-eszközön

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan telepítheti az 5. frissítést egy korábbi verziót futtató StorSimple-eszközön a Azure Portal és a gyorsjavítási módszer használatával. A gyorsjavítási módszer akkor használatos, ha az 5. frissítést a 3. előzetes verziót futtató eszközön kísérli meg telepíteni. A gyorsjavítási módszert akkor is használja a rendszer, ha egy átjáró a StorSimple-eszköztől eltérő hálózati adapteren van konfigurálva, és az 1. frissítés előtti verzióról próbál frissíteni.

Az 5. frissítés tartalmazza az eszköz szoftverét, a Storport és a ûrkikötõt, az operációs rendszer biztonsági frissítéseit és az operációs rendszer frissítéseit, valamint a lemez belső  Az eszköz szoftver-, ûrkikötõt-, Storport-, biztonsági és egyéb operációsrendszer-frissítései nem zavaró frissítések. A nem zavaró vagy rendszeres frissítések a Azure Portal vagy a gyorsjavítási módszer használatával alkalmazhatók. A lemez belső vezérlőprogram-frissítései zavaró frissítések, amelyek akkor lépnek életbe, ha az eszköz karbantartási üzemmódban van, és az eszköz Windows PowerShell felületét használja.

> [!IMPORTANT]
> * Az 5. frissítés egy kötelező frissítés, amelyet azonnal telepíteni kell. További információ: 5. [frissítéshez tartozó kibocsátási megjegyzések](storsimple-update5-release-notes.md).
> * A telepítés előtt manuális és automatikus ellenőrzéseket végeznek az eszközök állapotának meghatározásához a hardver állapota és a hálózati kapcsolat alapján. Ezeket az előzetes ellenőrzéseket csak akkor hajtja végre, ha a Azure Portalről alkalmazza a frissítéseket.
> * Azt javasoljuk, hogy a 3. frissítés előtti verziót futtató eszközök frissítésekor telepítse a frissítéseket a gyorsjavítási módszer használatával. Ha bármilyen problémába ütközik, [Jelentkezzen be egy támogatási jegyet](storsimple-8000-contact-microsoft-support.md).
> * Javasoljuk, hogy telepítse a szoftvert és az egyéb rendszeres frissítéseket a Azure Portal használatával. Az eszköz Windows PowerShell-felületén (a frissítések telepítéséhez) csak akkor kell megjelennie, ha a portálon nem sikerül a frissítés előtti átjáró-ellenőrzés. Attól függően, hogy melyik verziót szeretné frissíteni, a frissítések telepítése 4 órát is igénybe vehet (vagy nagyobb). A karbantartási mód frissítéseit az eszköz Windows PowerShell-felületén keresztül kell telepíteni. Mivel a karbantartási mód frissítései nem zavaró frissítések, ezek az eszközök leállási idejét eredményezik.
> * Ha a választható StorSimple Snapshot Manager futtatja, győződjön meg arról, hogy az eszköz frissítése előtt frissítette a Snapshot Manager verzióját az 5. frissítésre.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Telepítse az 5. frissítést a Azure Portal használatával
A következő lépések végrehajtásával frissítheti az eszközt az [5. frissítéssel](storsimple-update5-release-notes.md).

> [!NOTE]
> A Microsoft további diagnosztikai adatokat kér le az eszközről. Ennek eredményeképpen, amikor az operatív csapatunk olyan eszközöket azonosít, amelyekkel problémák léptek fel, jobban felkészültünk az adatok gyűjtésére az eszközről és a problémák diagnosztizálására.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Ellenőrizze, hogy az eszközön fut-e az **StorSimple 8000 Series Update 5 (6.3.9600.17845)**. A **legutóbbi frissítés dátumát** módosítani kell.

Ekkor láthatja, hogy a karbantartási mód frissítései elérhetők (ez az üzenet akár 24 óráig is megjelenhet a frissítések telepítése után). A karbantartási mód frissítésének telepítésének lépései részletesen olvashatók a következő szakaszban.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Az 5-ös frissítés telepítése gyorsjavításként

A gyorsjavítási módszer használatával frissíthető szoftverek a következők:

* Frissítés 0,1, 0,2, 0,3
* 1. frissítés, 1,1, 1,2
* 2. frissítés, 2,1, 2,2
* 3. frissítés, 3,1
* 4-es frissítés

> [!NOTE] 
> Az 5. frissítés telepítésének ajánlott módja a Azure Portal, amikor a 3. vagy újabb verzióról próbál frissíteni. Ha a 3. frissítés előtt verziót futtató eszközt frissít, használja ezt az eljárást. Ezt az eljárást akkor is használhatja, ha nem sikerül az átjáró ellenőrzése, amikor a Azure Portalon keresztül kísérli meg telepíteni a frissítéseket. Az ellenőrzés sikertelen, ha olyan átjáróval rendelkezik, amely nem adat 0 hálózati adapterhez van rendelve, és az eszközön az 1. frissítésnél korábbi szoftververzió fut.

A gyorsjavítási módszer a következő három lépést foglalja magában:

1. Töltse le a gyorsjavításokat a Microsoft Update-katalógusból.
2. Telepítse és ellenőrizze a normál módú gyorsjavításokat.
3. Telepítse és ellenőrizze a karbantartási mód gyorsjavítását.

#### <a name="download-updates-for-your-device"></a>Az eszköz frissítéseinek letöltése

A következő gyorsjavításokat le kell töltenie és telepítenie kell az előírt sorrendben és a javasolt mappákban:

| Sorrend | KB | Leírás | Frissítéstípus | Telepítés időpontja |Telepítés mappába|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Szoftverfrissítés<br> _HcsSoftwareUpdate.exe_ és _CisMSDAgent.exe_ letöltése |Szabályos <br></br>Nem zavaró |~ 25 perc |FirstOrderUpdate|

Ha a 4. frissítést futtató eszközről frissít, csak az operációs rendszer összegző frissítéseit kell második sorrendi frissítésként telepíteni.

| Sorrend | KB | Leírás | Frissítéstípus | Telepítés időpontja |Telepítés mappába|
| --- | --- | --- | --- | --- | --- |
| 2a. |KB4025336 |Operációs rendszer összegző frissítései csomag <br> A Windows Server 2012 R2 verziójának letöltése |Szabályos <br></br>Nem zavaró |- |SecondOrderUpdate|

Ha a telepítést a 3-as vagy korábbi frissítést futtató eszközről végzi, a kumulatív frissítések mellett telepítse a következőt is.

| Sorrend | KB | Leírás | Frissítéstípus | Telepítés időpontja |Telepítés mappába|
| --- | --- | --- | --- | --- | --- |
| 2b. |KB4011841 <br> KB4011842 |LSI-illesztőprogram és belső vezérlőprogram frissítései <br> USM belső vezérlőprogram frissítése (3,38-es verzió) |Szabályos <br></br>Nem zavaró |~ 3 óra <br> (tartalmazza a következőt: 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Operációs rendszer biztonsági frissítései csomag <br> A Windows Server 2012 R2 verziójának letöltése |Szabályos <br></br>Nem zavaró |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Operációs rendszer frissítési csomagja <br> A Windows Server 2012 R2 verziójának letöltése |Szabályos <br></br>Nem zavaró |- |SecondOrderUpdate|


Előfordulhat, hogy a lemez belső vezérlőprogram-frissítéseit is telepítenie kell az előző táblázatokban látható összes frissítésre. A parancsmag futtatásával ellenőrizheti, hogy szüksége van-e a lemez belső vezérlőprogram-frissítéseire `Get-HcsFirmwareVersion` . Ha a következő belső vezérlőprogram-verziókat futtatja:,,,,,, `XMGJ` `XGEG` `KZ50` `F6C2` `VR08` `N003` `0107` akkor nem kell telepítenie ezeket a frissítéseket.

| Sorrend | KB | Leírás | Frissítéstípus | Telepítés időpontja | Telepítés mappába|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Lemez belső vezérlőprogramja |Karbantartás <br></br>Zavaró |~ 30 perc | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Ha a 4. frissítésből frissíti a frissítést, a teljes telepítési idő 4 óra.
> * Mielőtt alkalmazza ezt az eljárást a frissítés alkalmazásához, győződjön meg arról, hogy mind az Eszközkezelő online, mind a hardver-összetevők kifogástalan állapotban vannak.

A gyorsjavítások letöltéséhez és telepítéséhez hajtsa végre a következő lépéseket.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Következő lépések
További információ az [5. frissítés kiadásáról](storsimple-update5-release-notes.md).

