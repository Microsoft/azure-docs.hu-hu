---
title: Azure AD-hitelesítés a Eszközkezelő StorSimple 8000-es verziójában
description: Ismerteti, hogyan használható az HRE-alapú hitelesítés a szolgáltatáshoz, új regisztrációs kulcs előállítása, valamint az eszközök manuális regisztrálásának elvégzése.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: b09d68e7859a787c05a2fc62294f081c4345ae08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99805040"
---
# <a name="use-azure-active-directory-ad-authentication-for-your-storsimple"></a>Azure Active Directory (AD) hitelesítés használata a StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő szolgáltatás Microsoft Azure fut, és több StorSimple-eszközhöz csatlakozik. A mai napig a StorSimple Eszközkezelő Service egy Access Control szolgáltatást (ACS) használt a szolgáltatás hitelesítéséhez a StorSimple-eszközön. Az ACS-mechanizmus hamarosan elavult, és egy Azure Active Directory (HRE) hitelesítéssel lesz lecserélve. További információért látogasson el a következő közlemények az ACS-elavulás és a HRE-hitelesítés használata című részhez.

- [Az Azure ACS jövője Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [A Microsoft Access Control Service közelgő módosításai](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Ez a cikk ismerteti a HRE-hitelesítés és a kapcsolódó új szolgáltatás regisztrációs kulcsának részleteit, valamint a tűzfalszabályok StorSimple-eszközökre vonatkozó módosításait. A cikkben szereplő információk csak a StorSimple 8000 Series-eszközökre vonatkoznak.

A HRE hitelesítés az 5. vagy újabb verziót futtató StorSimple 8000 sorozatú eszközön történik. A HRE-hitelesítés bevezetése miatt a következő változások történnek:

- Tűzfalszabályok URL-mintái
- Szolgáltatás regisztrációs kulcsa.

Ezeket a módosításokat a következő szakaszokban részletesen ismertetjük.

## <a name="url-changes-for-aad-authentication"></a>HRE-hitelesítés URL-változásai

Annak biztosítása érdekében, hogy a szolgáltatás HRE-alapú hitelesítést használjon, az összes felhasználónak tartalmaznia kell az új hitelesítési URL-címeket a tűzfalszabályok között.

StorSimple 8000 sorozat használata esetén győződjön meg arról, hogy a tűzfalszabályok a következő URL-címet tartalmazzák:

| URL-minta                         | Felhőbeli | Összetevő/funkció         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |HRE hitelesítési szolgáltatás      |
| `https://login.microsoftonline.us` | Egyesült Államok – Államigazgatás |HRE hitelesítési szolgáltatás      |

Az StorSimple 8000 Series-eszközök URL-mintáinak teljes listáját a [Tűzfalszabályok URL-mintái](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules)című részében teheti meg.

Ha a tűzfalszabály nem tartalmazza a hitelesítési URL-címet az elavult dátumon túl, és az eszköz az 5. frissítést futtatja, a felhasználók egy URL-riasztást látnak. A felhasználóknak tartalmazniuk kell az új hitelesítési URL-címet. Ha az eszköz az 5. frissítés előtti verziót futtatja, a felhasználók szívverési riasztást látnak. A StorSimple-eszköz minden esetben nem tud hitelesíteni a szolgáltatással, és a szolgáltatás nem tud kommunikálni az eszközzel.

## <a name="device-version-and-authentication-changes"></a>Az eszköz verziószáma és a hitelesítés módosítása

Ha StorSimple 8000 sorozatú eszközt használ, a következő táblázat segítségével határozza meg, hogy milyen műveletet kell végrehajtania a futtatott eszköz szoftverének verziójától függően.

| Ha az eszköz fut| Végezze el a következő műveletet                                    |
|--------------------------|------------------------|
| 5-ös vagy újabb frissítése, és az eszköz offline állapotban van. <br> Megjelenik egy riasztás, amely szerint az URL-cím nincs jóváhagyva.|1. módosítsa a tűzfalszabályok használatát a hitelesítési URL-cím belefoglalásához. Lásd: [hitelesítési URL-címek](#url-changes-for-aad-authentication).<br>2. [szerezze be a HRE regisztrációs kulcsot a szolgáltatásból](#aad-based-registration-keys).<br>3. [kapcsolódjon az StorSimple 8000 Series eszköz Windows PowerShell-felületéhez](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. a `Redo-DeviceRegistration` parancsmag használatával regisztrálja az eszközt a Windows powershellen keresztül. Adja meg az előző lépésben kapott kulcsot.|
| 5-ös vagy újabb, illetve az eszköz online állapotának frissítése.| Semmit nem kell tenni.                                       |
| A 4-es vagy korábbi frissítés, és az eszköz offline állapotban van. |1. módosítsa a tűzfalszabályok használatát a hitelesítési URL-cím belefoglalásához.<br>2. [töltse le az 5. frissítést a Catalog Server használatával](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [az 5. frissítést alkalmazza a gyorsjavítási módszer használatával](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [szerezze be a HRE regisztrációs kulcsot a szolgáltatásból](#aad-based-registration-keys).<br>5. [kapcsolódjon az StorSimple 8000 Series eszköz Windows PowerShell-felületéhez](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. a `Redo-DeviceRegistration` parancsmag használatával regisztrálja az eszközt a Windows powershellen keresztül. Adja meg az előző lépésben kapott kulcsot.|
| A 4-es vagy korábbi frissítés, az eszköz pedig online állapotban van. |A tűzfalszabályok módosításával adja meg a hitelesítési URL-címet.<br> Telepítse az 5. frissítést a Azure Portal.              |
| Állítsa vissza a gyári verziót az 5. frissítés előtt.      |A portálon egy HRE-alapú regisztrációs kulcs látható, miközben az eszköz régebbi szoftvert futtat. Kövesse az előző forgatókönyv lépéseit, amikor az eszköz a 4-es vagy korábbi frissítést futtatja.              |

## <a name="aad-based-registration-keys"></a>HRE-alapú regisztrációs kulcsok

Az 5. frissítés megkezdése a StorSimple 8000 Series-eszközökhöz új HRE-alapú regisztrációs kulcsokat használ. A regisztrációs kulcsok használatával regisztrálja StorSimple Eszközkezelő szolgáltatását az eszközön.

Ha a 4-es vagy korábbi frissítést futtató StorSimple 8000 sorozatú eszközt használ, nem használhatja az új HRE szolgáltatás regisztrációs kulcsait.
Ebben a forgatókönyvben újra kell létrehoznia a szolgáltatás regisztrációs kulcsát. A kulcs újralétrehozása után az új kulcs az összes további eszköz regisztrálására szolgál. A régi kulcs már nem érvényes.

- Az új HRE regisztrációs kulcs 3 nap után lejár.
- A HRE regisztrációs kulcsai csak az 5. vagy újabb frissítést futtató StorSimple 8000 sorozatú eszközökön működnek.
- A HRE regisztrációs kulcsa hosszabb a megfelelő ACS regisztrációs kulcsoknál.

A HRE szolgáltatás regisztrációs kulcsának létrehozásához hajtsa végre az alábbi lépéseket.

#### <a name="to-generate-the-aad-service-registration-key"></a>A HRE szolgáltatás regisztrációs kulcsának előállítása

1. A **StorSimple Eszközkezelő** lépjen a **felügyeleti &gt;** **kulcsok** elemre. A keresési sáv használatával is megkeresheti a _kulcsokat_.
    
2. Kattintson a **kulcs előállítása** elemre.

    ![Kattintson az újragenerált elemre.](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Másolja az új kulcsot. A régebbi kulcs többé nem fog működni.

    ![Újragenerált megerősítés megerősítése](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Ha a StorSimple 8000 Series eszközhöz regisztrált szolgáltatáshoz hoz létre StorSimple Cloud Appliance, ne hozzon létre regisztrációs kulcsot, amíg a létrehozás folyamatban van. Várjon, amíg a létrehozás befejeződik, majd hozza létre a regisztrációs kulcsot.

## <a name="next-steps"></a>Következő lépések

* További információ az [StorSimple 8000 Series-eszközök](storsimple-8000-deployment-walkthrough-u2.md)üzembe helyezéséről.
