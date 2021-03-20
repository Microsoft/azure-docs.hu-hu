---
title: Feltételes hozzáférés – hozzáférés tiltása – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása a következőhöz
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e0801daa5bf83889be87987d440e377287b5ea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92366190"
---
# <a name="conditional-access-block-access"></a>Feltételes hozzáférés: hozzáférés letiltása

A konzervatív Felhőbeli áttelepítési módszerekkel rendelkező szervezetek esetében az összes házirend blokkolható lehetőség. 

> [!CAUTION]
> A blokkolási szabályzatok helytelen konfigurációja olyan szervezetek számára is járhat, amelyek kizárhatók a Azure Portalból.

Ezek a szabályzatok, mint például a nem szándékolt mellékhatások lehetnek. Az engedélyezése előtt elengedhetetlen a megfelelő tesztelés és ellenőrzés. A rendszergazdáknak olyan eszközöket kell használniuk, mint például a [feltételes hozzáférés jelentésének módja](concept-conditional-access-report-only.md) és [a What if eszköz feltételes hozzáféréssel a](what-if-tool.md) módosítások végrehajtásakor.

## <a name="user-exclusions"></a>Felhasználói kizárások

A feltételes hozzáférési szabályzatok hatékony eszközök, ezért javasoljuk, hogy a szabályzatból kizárja a következő fiókokat:

* **Vészhelyzeti hozzáférés** vagy **megszakítás-Glass** fiókok a bérlői szintű fiókok zárolásának megakadályozása érdekében. Abban az esetben, ha nem valószínű, hogy az összes rendszergazda ki van zárva a bérlőből, a vészhelyzeti hozzáférésű rendszergazdai fiók segítségével bejelentkezhet a bérlőnek a hozzáférés helyreállításához szükséges lépésekkel.
   * További információt a következő cikkben talál: [vészhelyzeti hozzáférési fiókok kezelése az Azure ad-ben](../roles/security-emergency-access.md).
* **Szolgáltatásfiókok és** **egyszerű szolgáltatások**, például a Azure ad Connect szinkronizálási fiók. A szolgáltatásfiókok olyan nem interaktív fiókok, amelyek nincsenek egy adott felhasználóhoz kötve. Ezeket általában olyan háttér-szolgáltatások használják, amelyek lehetővé teszik a programozott hozzáférést az alkalmazásokhoz, de a rendszerekre is felhasználják a felügyeleti célokra való bejelentkezést. Ezeket a szolgáltatási fiókokat ki kell zárni, mert az MFA nem hajtható végre programozott módon. Az egyszerű szolgáltatások által kezdeményezett hívásokat nem blokkolja a feltételes hozzáférés.
   * Ha a szervezete ezeket a fiókokat parancsfájlokban vagy kódban használja, érdemes lehet a [felügyelt identitásokkal](../managed-identities-azure-resources/overview.md)helyettesíteni őket. Ideiglenes megkerülő megoldásként kizárhatja ezeket a fiókokat az alapkonfiguráció házirendjéből.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

A következő lépések segítséget nyújtanak a feltételes hozzáférési szabályzatok létrehozásához, hogy letiltsák a hozzáférést az összes alkalmazáshoz, kivéve az [Office 365](concept-conditional-access-cloud-apps.md#office-365) -at, ha a felhasználók nem megbízható hálózaton vannak. Ezek a szabályzatok a [csak jelentési üzemmódba](howto-conditional-access-insights-reporting.md) kerülnek, így a rendszergazdák meghatározhatják, hogy milyen hatással lesznek a meglévő felhasználókra. Ha a rendszergazdák kényelmesek, hogy a szabályzatok a kívánt módon érvényesek **, a** következőre válthatnak.

Az első házirend blokkolja a hozzáférést az összes alkalmazáshoz, kivéve Microsoft 365 alkalmazásokat, ha nem megbízható helyen van.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat** lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás** területen válassza a **minden felhasználó** lehetőséget.
   1. A **kizárás** területen válassza a **felhasználók és csoportok** lehetőséget, majd válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat. 
   1. Válassza a **Kész** lehetőséget.
1. A **Cloud apps vagy műveletek** területen válassza ki a következő beállításokat:
   1. A **Belefoglalás** területen válassza a **minden felhőalapú alkalmazás** lehetőséget.
   1. A **kizárás** területen válassza az **Office 365** lehetőséget, válassza a **kiválasztás** lehetőséget, majd válassza a **kész** lehetőséget.
1. **Feltételek**:
   1. A **feltételek**  >  **helye** alatt.
      1. **Konfigurálás** beállítása **Igen** értékre
      1. A **Belefoglalás** területen válassza ki **a kívánt helyet**.
      1. A **kizárás** területen válassza ki **az összes megbízható helyet**.
      1. Válassza a **Kész** lehetőséget.
   1. Az **ügyfélalkalmazások (előzetes verzió)** területen állítsa az **Igen** értékre a **configure** beállítást, majd válassza a **kész**, majd a **kész** lehetőséget.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza a **hozzáférés letiltása**, majd a **kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **házirendet** **csak jelentésre**.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

Az alábbiakban egy második szabályzatot hozhat létre a többtényezős hitelesítés megkövetelése vagy egy megfelelő eszköz számára a Microsoft 365 felhasználói számára.

1. Válassza az **új szabályzat** lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás** területen válassza a **minden felhasználó** lehetőséget.
   1. A **kizárás** területen válassza a **felhasználók és csoportok** lehetőséget, majd válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat. 
   1. Válassza a **Kész** lehetőséget.
1. A **Cloud apps vagy műveletek** területen  >  válassza az **alkalmazások kiválasztása**, az **Office 365** lehetőséget, majd a **kiválasztás**, majd a **kész** lehetőséget.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza a **hozzáférés engedélyezése** lehetőséget.
   1. Válassza a **többtényezős hitelesítés megkövetelése** és az **eszköz megfelelőként való megjelölésének megkövetelése** **jelölőnégyzetet.**
   1. Győződjön meg arról, hogy **az összes kijelölt vezérlő** be van jelölve.
   1. Válassza a **Kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **házirendet** **csak jelentésre**.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-insights-reporting.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
