---
title: Megosztott eszköz mód Android-eszközökhöz
titleSuffix: Microsoft identity platform | Azure
description: Megtudhatja, hogyan engedélyezheti a megosztott eszközök üzemmódját, hogy az élvonalbeli feldolgozók megosszák egy Android-eszközt
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: marsma
ms.reviewer: hahamil
ms.custom: aaddev, identitypla | Azuretformtop40
ms.openlocfilehash: bafd6dade626376f216a271c7b3cadc9b429989a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578821"
---
# <a name="shared-device-mode-for-android-devices"></a>Megosztott eszköz mód Android-eszközökhöz

A feldolgozók, például a kiskereskedelmi munkatársak, a hajózószemélyzet tagjai és a terepi szolgáltatások feldolgozói gyakran közös mobileszköz használatával végzik munkájukat. Ez akkor válik problémamentesnek, amikor megkezdik a jelszavak megosztását, és a megosztott eszközön lévő ügyfél-és üzleti adatokhoz a PIN-kódok számát használják

A megosztott eszköz mód lehetővé teszi az Android-eszközök konfigurálását úgy, hogy az egyszerűen több alkalmazott számára is megosztható legyen. Az alkalmazottak gyorsan jelentkezhetnek be és érhetik el az ügyfelek adatait. Ha végzett a váltással vagy a feladattal, akkor kijelentkezhetnek az eszközről, és azonnal készen állnak a következő alkalmazott használatára.

A megosztott eszköz mód az eszköz Microsoft Identity-beli felügyeletét is biztosítja.

Megosztott eszköz üzemmódú alkalmazás létrehozásához a fejlesztők és a felhőalapú eszközök rendszergazdái együttesen működnek:

- A fejlesztők egyfiókos alkalmazást írnak (a több fiókból álló alkalmazások nem támogatottak a megosztott eszköz módban), hozzáadhatók `"shared_device_mode_supported": true` az alkalmazás konfigurációjához, és kódot írhatnak az olyan dolgok kezelésére, mint például a megosztott eszközök kijelentkezése.
- Az eszközök rendszergazdái az eszköz megosztását a hitelesítő alkalmazás telepítésével, valamint a hitelesítő alkalmazás használatával a megosztott módba állításával készíti el. A [hitelesítő alkalmazás](../user-help/user-help-auth-app-overview.md)használatával csak a [Cloud Device rendszergazdai](../roles/permissions-reference.md#cloud-device-administrator) szerepkörrel rendelkező felhasználók helyezhetnek el egy eszközt megosztott módba. A Azure Portal a (z) **Azure Active Directory**  >  **szerepkörök és rendszergazdák**  >  **felhőalapú eszköz rendszergazdája** segítségével konfigurálhatja a szervezeti szerepkörök tagságát.

 Ez a cikk elsősorban a fejlesztőknek szól.

## <a name="single-vs-multiple-account-applications"></a>Egyetlen vs több fiókból származó alkalmazás

A Microsoft Authentication Library SDK (MSAL) használatával írt alkalmazások egyetlen fiókot vagy több fiókot kezelhetnek. Részletekért lásd: [egyfiókos mód vagy több fiókos mód](single-multi-account.md). Az alkalmazáshoz elérhető Microsoft Identity platform-funkciók attól függően változnak, hogy az alkalmazás egyfiókos módban vagy több fiókos módban fut-e.

**A megosztott eszköz üzemmódú alkalmazások csak egyfiókos módban működnek**.

> [!IMPORTANT]
> A több fiókból álló üzemmódot támogató alkalmazások nem futtathatók megosztott eszközön. Ha egy alkalmazott olyan alkalmazást tölt be, amely nem támogatja az egyfiókos üzemmódot, a megosztott eszközön nem fog futni.
>
> A MSAL SDK kiadása előtt írt alkalmazások több fiókos módban futnak, és frissíteni kell az egyfiókos mód támogatásához, mielőtt azok futtathatók legyenek egy megosztott módú eszközön.

**Az egyfiókos és a többszörös fiókok támogatása**

Az alkalmazás a személyes eszközökön és a megosztott eszközökön való futtatás támogatásához is felépíthető. Ha az alkalmazás jelenleg több fiókot támogat, és a megosztott eszköz módot szeretné támogatni, adja hozzá az egyfiókos mód támogatását.

Azt is megteheti, hogy az alkalmazás attól függően módosítja a viselkedését, hogy milyen típusú eszközön fut. `ISingleAccountPublicClientApplication.isSharedDevice()`A használatával meghatározhatja, hogy mikor fusson egyfiókos módban.

Két különböző interfész látható, amely az alkalmazás által használt eszköz típusát jelöli. Ha alkalmazás-példányt kér a MSAL alkalmazás-előállítótól, a rendszer automatikusan megadja a megfelelő alkalmazásobjektum-objektumot.

A következő objektummodell szemlélteti a kapott objektum típusát, valamint azt, hogy egy megosztott eszköz kontextusában mit jelent:

![nyilvános ügyfélalkalmazás öröklési modellje](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Az objektum beolvasása után be kell állítania a megfelelő felületet `PublicClientApplication` . A következő kód több fiók üzemmódot vagy egyfiókos üzemmódot keres, és megfelelő módon veti fel az Application objektumot:

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

Az alábbi különbségek attól függően változnak, hogy az alkalmazás megosztott vagy személyes eszközön fut-e:

|  | Megosztott üzemmódú eszköz  | Személyes eszköz |
|---------|---------|---------|
| **Fiókok**     | Egyetlen fiók | Több fiók |
| **Bejelentkezés** | Globális | Globális |
| **Kijelentkezés** | Globális | Minden alkalmazás szabályozhatja, hogy a kijelentkezés helyi-e az alkalmazás vagy az alkalmazások családja számára. |
| **Támogatott fióktípusok** | Csak munkahelyi fiókok | A személyes és munkahelyi fiókok támogatottak  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Miért érdemes csak egy egyfiókos üzemmódot támogatni

Ha olyan alkalmazást ír, amely egy megosztott eszköz használatával csak Frontline-feldolgozók számára lesz felhasználva, javasoljuk, hogy az alkalmazás csak az egyfiókos üzemmódot támogassa. Ez magában foglalja a legtöbb olyan alkalmazást, amely a feladathoz tartozik, például a Medical Records-alkalmazások, a számlafogadó-alkalmazások és a legtöbb üzletági alkalmazás. Csak az egyfiókos üzemmód támogatása egyszerűsíti a fejlesztést, mivel nem kell megvalósítani a több fiókból álló alkalmazások részét képező további szolgáltatásokat.

## <a name="what-happens-when-the-device-mode-changes"></a>Mi történik az eszköz üzemmódjának módosításakor

Ha az alkalmazás több fiókból áll, és a rendszergazda megosztott eszköz módban helyezi üzembe az eszközt, az eszközön lévő összes fiók törlődik az alkalmazásból, és az alkalmazás egy egyfiókos üzemmódra vált.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Megosztott eszköz kijelentkezése és az alkalmazások teljes életciklusa

Amikor egy felhasználó kijelentkezik, el kell végeznie a felhasználó adatainak és adatainak védelméhez szükséges lépéseket. Ha például egy orvosi rekordokat tartalmazó alkalmazást készít, akkor győződjön meg arról, hogy ha a felhasználó kijelentkezik, akkor a rendszer törli a korábban megjelenített beteg rekordokat. Az alkalmazást fel kell készülnie erre, és minden alkalommal ellenőriznie kell, amikor belép az előtérben.

Ha az alkalmazás a MSAL használatával kijelentkezik egy megosztott módban lévő eszközön futó alkalmazásban, a bejelentkezett fiók és a gyorsítótárazott tokenek el lesznek távolítva az alkalmazásból és az eszközből is.

Az alábbi ábrán az alkalmazás teljes életciklusa látható, valamint az alkalmazás futása során esetlegesen előforduló gyakori események. A diagram a tevékenységek elindításának, beléptetésének és kijelentkezésének időpontját mutatja be, valamint azt, hogy az események, mint például a felfüggesztés, a folytatás és a tevékenység leállítása.

![Megosztott eszközök alkalmazásának életciklusa](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Következő lépések

Próbálja ki a [Shared-Device üzemmódot az Android-alkalmazás](tutorial-v2-shared-device-mode.md) oktatóanyagában, amely bemutatja, hogyan futtathat egy Frontline Worker alkalmazást egy megosztott módú Android-eszközön.
