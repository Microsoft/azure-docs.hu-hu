---
title: Feltételes hozzáférés – MFA megkövetelése minden felhasználó számára – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása, amely megköveteli, hogy minden felhasználó végrehajtsa a többtényezős hitelesítést
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e6185c4bde71285fc163cae2af46f64ba052195
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994757"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Feltételes hozzáférés: MFA megkövetelése minden felhasználó számára

Alex Weinert, a Microsoft Identity Security könyvtára, amely a [PA $ $Word](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)blogbejegyzésében említi, nem számít:

> A jelszó nem számít, de az MFA-t is! Tanulmányunk alapján a fiókja több mint 99,9%-kal kevésbé valószínű, ha MFA-t használ.

A cikk útmutatása alapján a szervezet létrehoz egy kiegyensúlyozott MFA-szabályzatot a környezet számára.

## <a name="user-exclusions"></a>Felhasználói kizárások

A feltételes hozzáférési szabályzatok hatékony eszközök, ezért javasoljuk, hogy a szabályzatból kizárja a következő fiókokat:

* **Vészhelyzeti hozzáférés** vagy **megszakítás-Glass** fiókok a bérlői szintű fiókok zárolásának megakadályozása érdekében. Abban az esetben, ha nem valószínű, hogy az összes rendszergazda ki van zárva a bérlőből, a vészhelyzeti hozzáférésű rendszergazdai fiók segítségével bejelentkezhet a bérlőnek a hozzáférés helyreállításához szükséges lépésekkel.
   * További információt a következő cikkben talál: [vészhelyzeti hozzáférési fiókok kezelése az Azure ad-ben](../roles/security-emergency-access.md).
* **Szolgáltatásfiókok és** **egyszerű szolgáltatások**, például a Azure ad Connect szinkronizálási fiók. A szolgáltatásfiókok olyan nem interaktív fiókok, amelyek nincsenek egy adott felhasználóhoz kötve. Ezeket általában olyan háttér-szolgáltatások használják, amelyek lehetővé teszik a programozott hozzáférést az alkalmazásokhoz, de a rendszerekre is felhasználják a felügyeleti célokra való bejelentkezést. Ezeket a szolgáltatási fiókokat ki kell zárni, mert az MFA nem hajtható végre programozott módon. Az egyszerű szolgáltatások által kezdeményezett hívásokat nem blokkolja a feltételes hozzáférés.
   * Ha a szervezete ezeket a fiókokat parancsfájlokban vagy kódban használja, érdemes lehet a [felügyelt identitásokkal](../managed-identities-azure-resources/overview.md)helyettesíteni őket. Ideiglenes megkerülő megoldásként kizárhatja ezeket a fiókokat az alapkonfiguráció házirendjéből.

## <a name="application-exclusions"></a>Alkalmazás-kizárások

A szervezeteknek sok Felhőbeli alkalmazása lehet. Előfordulhat, hogy nem mindegyik alkalmazásnak egyenlő a biztonsága. Előfordulhat például, hogy a bérszámfejtési és a részvételi alkalmazások MFA-t igényelnek, de a cafeteria valószínűleg nem. A rendszergazdák dönthetnek úgy, hogy bizonyos alkalmazásokat kizárnak a szabályzatból.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

A következő lépések segítenek létrehozni egy feltételes hozzáférési szabályzatot, amely megköveteli, hogy minden felhasználó elvégezzen többtényezős hitelesítést.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat** lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések** alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás** területen válassza a **minden felhasználó** lehetőséget.
   1. A **kizárás** területen válassza a **felhasználók és csoportok** lehetőséget, majd válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat. 
   1. Válassza a **Kész** lehetőséget.
1. A **Cloud apps vagy a műveletek** területen válassza a  >   **minden felhőalapú alkalmazás** lehetőséget.
   1. A **kizárás** területen válassza ki azokat az alkalmazásokat, amelyek nem igénylik a többtényezős hitelesítést.
1. Az   >  **ügyfélalkalmazások (előzetes verzió)** feltételek alatt **válassza ki a házirend** hatálya alá tartozó ügyfélalkalmazások kiválasztása lehetőséget az összes kijelölt alapértelmezett érték elhagyásához, majd válassza a **kész** lehetőséget.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza a **hozzáférés biztosítása**, a **többtényezős hitelesítés megkövetelése**, majd a **kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva** értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

### <a name="named-locations"></a>Nevesített helyek

A szervezetek dönthetnek úgy, hogy a feltételes hozzáférési szabályzatoknak **nevezett helyként** ismert hálózati helyet foglalnak magukban. Ezek a nevesített helyek olyan megbízható IPv4-hálózatokat tartalmazhatnak, mint a Főiroda helyének. Az elnevezett helyek konfigurálásával kapcsolatos további információkért tekintse [meg a feltételes hozzáférés Azure Active Directory a hely feltételeit](location-condition.md) ismertető cikket.

A fenti példában szereplő házirendben a szervezet dönthet úgy, hogy nem igényli a többtényezős hitelesítést, ha a vállalati hálózatról fér hozzá a felhőalapú alkalmazásokhoz. Ebben az esetben a következő konfigurációt vehetik fel a szabályzatba:

1. A **hozzárendelések** területen válassza a **feltételek**  >  **helyszínek** lehetőséget.
   1. Konfigurálja az **Igen értéket**.
   1. Adjon meg **bármilyen helyet**.
   1. **Az összes megbízható helyszín** kizárása.
   1. Válassza a **Kész** lehetőséget.
1. Válassza a **Kész** lehetőséget.
1. **Mentse** a szabályzat módosításait.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-insights-reporting.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
