---
title: Új hitelesítés a StorSimple virtuális tömbökhöz
description: Ismerje meg a HRE-hitelesítést, a társított új szolgáltatás regisztrációs kulcsát, valamint a tűzfalszabályok módosításait a StorSimple-eszközökre.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 75332498ac59dc46a7a079eff4c25e02b2a6cb9b
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986937"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Az új hitelesítés használata a StorSimple

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

A StorSimple Eszközkezelő szolgáltatás Microsoft Azure fut, és több StorSimple virtuális tömbhöz csatlakozik. A mai napig a StorSimple Eszközkezelő Service egy Access Control szolgáltatást (ACS) használt a szolgáltatás hitelesítéséhez a StorSimple-eszközön. Az ACS-mechanizmus hamarosan elavult, és egy Azure Active Directory (HRE) hitelesítéssel lesz lecserélve.

A cikkben szereplő információk csak a StorSimple 1200 sorozatú virtuális tömbökre érvényesek. Ez a cikk ismerteti a HRE-hitelesítés és a kapcsolódó új szolgáltatás regisztrációs kulcsának részleteit, valamint a tűzfalszabályok StorSimple-eszközökre vonatkozó módosításait.

A HRE-hitelesítés a StorSimple virtuális tömbökben (1200-es modell) fut az 1. vagy újabb frissítést futtatva.

A HRE-hitelesítés bevezetése miatt a következő változások történnek:

- Tűzfalszabályok URL-mintái
- Szolgáltatás regisztrációs kulcsa.

Ezeket a módosításokat a következő szakaszokban részletesen ismertetjük.

## <a name="url-changes-for-aad-authentication"></a>HRE-hitelesítés URL-változásai

Annak biztosítása érdekében, hogy a szolgáltatás HRE-alapú hitelesítést használjon, az összes felhasználónak tartalmaznia kell az új hitelesítési URL-címeket a tűzfalszabályok között.

StorSimple virtuális tömb használata esetén győződjön meg arról, hogy a tűzfalszabályok a következő URL-címet tartalmazzák:

| URL-minta                         | Felhőbeli | Összetevő/funkció         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |HRE hitelesítési szolgáltatás      |
| `https://login.microsoftonline.us` | Egyesült Államok – Államigazgatás |HRE hitelesítési szolgáltatás      |

A StorSimple virtuális tömbök URL-mintáinak teljes listájáért keresse fel a [Tűzfalszabályok URL-mintáit](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Ha a tűzfalszabályok nem tartalmazzák a hitelesítési URL-címet az elavultság dátuma után, a felhasználók kritikus riasztást látnak arról, hogy a StorSimple-eszközük nem tudta hitelesíteni a szolgáltatást. A szolgáltatás nem fog tudni kommunikálni az eszközzel. Ha a felhasználók ezt a riasztást látják, fel kell venniük az új hitelesítési URL-címet. A riasztással kapcsolatos további információkért nyissa meg a [riasztások használata a StorSimple-eszköz figyeléséhez](storsimple-virtual-array-manage-alerts.md#networking-alerts)című témakört.

## <a name="device-version-and-authentication-changes"></a>Az eszköz verziószáma és a hitelesítés módosítása

Ha StorSimple virtuális tömböt használ, a következő táblázat segítségével határozza meg, hogy milyen műveletet kell végrehajtania a futtatott eszköz szoftverének verziójától függően.

| Ha az eszköz fut  | Végezze el a következő műveletet                                    |
|----------------------------|--------------------------------------------------------------|
| A 1,0-es vagy újabb frissítés offline állapotú. <br> Megjelenik egy riasztás arról, hogy az URL-cím nem allowlisted.| 1. módosítsa a tűzfalszabályok használatát a hitelesítési URL-cím belefoglalásához. Lásd: [hitelesítési URL-címek](#url-changes-for-aad-authentication). <br> 2. [szerezze be a HRE regisztrációs kulcsot a szolgáltatásból](#aad-based-registration-keys). <br> 3. hajtsa végre a 1-5-es lépéseket a [virtuális tömb Windows PowerShell-felületéhez való kapcsolódáshoz](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. a `Invoke-HcsReRegister` parancsmag használatával regisztrálja az eszközt a Windows powershellen keresztül. Adja meg az előző lépésben kapott kulcsot.|
| Frissítse a 1,0-es vagy újabb verziót, és az eszköz online állapotú.| Semmit nem kell tenni.                                       |
| Az 0,6-es vagy korábbi verzió frissítése, az eszköz offline állapotban van. | 1. [töltse le a 1,0-es frissítést a Catalog Server használatával](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [alkalmazza a 1,0-es frissítést a helyi webes felhasználói felületen](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [szerezze be a HRE regisztrációs kulcsot a szolgáltatásból](#aad-based-registration-keys). <br>4. hajtsa végre a 1-5-es lépéseket a [virtuális tömb Windows PowerShell-felületéhez való kapcsolódáshoz](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. a `Invoke-HcsReRegister` parancsmag használatával regisztrálja az eszközt a Windows powershellen keresztül. Adja meg az előző lépésben kapott kulcsot.|
| Az 0,6-es vagy korábbi frissítés, és az eszköz online állapotú | A tűzfalszabályok módosításával adja meg a hitelesítési URL-címet.<br> Telepítse a 1,0-es frissítést a Azure Portal. |

## <a name="aad-based-registration-keys"></a>HRE-alapú regisztrációs kulcsok

A StorSimple virtuális tömbök 1,0-es frissítésének megkezdése után új HRE-alapú regisztrációs kulcsok használatosak. A regisztrációs kulcsok használatával regisztrálja StorSimple Eszközkezelő szolgáltatását az eszközön.

Ha a 0,6-es vagy korábbi frissítést futtató StorSimple virtuális tömböket használ, nem használhatja az új HRE szolgáltatás regisztrációs kulcsait. Újra kell létrehoznia a szolgáltatás regisztrációs kulcsát. A kulcs újralétrehozása után az új kulcs az összes további eszköz regisztrálására szolgál. A régi kulcs már nem érvényes.

- Az új HRE regisztrációs kulcs 3 nap után lejár.
- A HRE-regisztrációs kulcsok csak az 1. vagy újabb frissítést futtató StorSimple 1200 sorozatú virtuális tömbökkel működnek. A StorSimple 8000 sorozatú eszköz HRE-regisztrációs kulcsa nem fog működni.
- A HRE regisztrációs kulcsa hosszabb a megfelelő ACS regisztrációs kulcsoknál.

A HRE szolgáltatás regisztrációs kulcsának létrehozásához hajtsa végre az alábbi lépéseket.

#### <a name="to-generate-the-aad-service-registration-key"></a>A HRE szolgáltatás regisztrációs kulcsának előállítása

1. A **StorSimple Eszközkezelő** lépjen a **felügyeleti &gt;** **kulcsok** elemre.
    
    ![Ugrás a kulcsokra](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Kattintson a **kulcs előállítása** elemre.

    ![Kattintson az újragenerált elemre.](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Másolja az új kulcsot. A régebbi kulcs már nem működik.

    ![Újragenerált megerősítés megerősítése](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Következő lépések

* További információ a [StorSimple virtuális tömb](storsimple-virtual-array-deploy1-portal-prep.md) üzembe helyezéséről
