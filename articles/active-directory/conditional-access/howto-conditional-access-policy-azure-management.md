---
title: Feltételes hozzáférés – MFA megkövetelése az Azure Management szolgáltatáshoz – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása a többtényezős hitelesítés megköveteléséhez az Azure felügyeleti feladataihoz
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
ms.openlocfilehash: 2e2b6b3e9a6bdead4e4da7f1a829698d86cfbf52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92366173"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Feltételes hozzáférés: MFA megkövetelése az Azure felügyeletéhez

A szervezetek különböző Azure-szolgáltatásokat használnak és felügyelik azokat Azure Resource Manager-alapú eszközökről, például:

* Azure Portal
* Azure PowerShell
* Azure CLI

Ezek az eszközök magas jogosultsági szintű hozzáférést biztosíthatnak az erőforrásokhoz, amelyek megváltoztathatják az előfizetés-alapú konfigurációkat, a szolgáltatás beállításait és az előfizetés számlázását. A Kiemelt erőforrások elleni védelem érdekében a Microsoft a többtényezős hitelesítés megkövetelését javasolja bármely olyan felhasználó számára, aki hozzáfér ezekhez az erőforrásokhoz.

## <a name="user-exclusions"></a>Felhasználói kizárások

A feltételes hozzáférési szabályzatok hatékony eszközök, ezért javasoljuk, hogy a szabályzatból kizárja a következő fiókokat:

* **Vészhelyzeti hozzáférés** vagy **megszakítás-Glass** fiókok a bérlői szintű fiókok zárolásának megakadályozása érdekében. Abban az esetben, ha nem valószínű, hogy az összes rendszergazda ki van zárva a bérlőből, a vészhelyzeti hozzáférésű rendszergazdai fiók segítségével bejelentkezhet a bérlőnek a hozzáférés helyreállításához szükséges lépésekkel.
   * További információt a következő cikkben talál: [vészhelyzeti hozzáférési fiókok kezelése az Azure ad-ben](../roles/security-emergency-access.md).
* **Szolgáltatásfiókok és** **egyszerű szolgáltatások**, például a Azure ad Connect szinkronizálási fiók. A szolgáltatásfiókok olyan nem interaktív fiókok, amelyek nincsenek egy adott felhasználóhoz kötve. Ezeket általában olyan háttér-szolgáltatások használják, amelyek lehetővé teszik a programozott hozzáférést az alkalmazásokhoz, de a rendszerekre is felhasználják a felügyeleti célokra való bejelentkezést. Ezeket a szolgáltatási fiókokat ki kell zárni, mert az MFA nem hajtható végre programozott módon. Az egyszerű szolgáltatások által kezdeményezett hívásokat nem blokkolja a feltételes hozzáférés.
   * Ha a szervezete ezeket a fiókokat parancsfájlokban vagy kódban használja, érdemes lehet a [felügyelt identitásokkal](../managed-identities-azure-resources/overview.md)helyettesíteni őket. Ideiglenes megkerülő megoldásként kizárhatja ezeket a fiókokat az alapkonfiguráció házirendjéből.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

A következő lépések segítenek létrehozni egy feltételes hozzáférési szabályzatot, amely a többtényezős hitelesítés végrehajtásához hozzáférést igényel a [Microsoft Azure felügyeleti](concept-conditional-access-cloud-apps.md#microsoft-azure-management) alkalmazáshoz.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat** lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések** alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás** területen válassza a **minden felhasználó** lehetőséget.
   1. A **kizárás** területen válassza a **felhasználók és csoportok** lehetőséget, majd válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat. 
   1. Válassza a **Kész** lehetőséget.
1. A **Cloud apps vagy műveletek** területen  >  válassza az **alkalmazások kiválasztása** lehetőséget, válassza a **Microsoft Azure felügyelet** lehetőséget, majd válassza a **kiválasztás** , majd a **kész** lehetőséget.
1. Az   >  **ügyfélalkalmazások (előzetes verzió)** feltételek alatt **válassza ki a házirend** hatálya alá tartozó ügyfélalkalmazások kiválasztása lehetőséget az összes kijelölt alapértelmezett érték elhagyásához, majd válassza a **kész** lehetőséget.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza a **hozzáférés biztosítása**, a **többtényezős hitelesítés megkövetelése**, majd a **kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva** értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-insights-reporting.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
