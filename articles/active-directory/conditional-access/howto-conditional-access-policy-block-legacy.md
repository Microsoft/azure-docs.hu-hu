---
title: Feltételes hozzáférés – örökölt hitelesítés tiltása – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása az örökölt hitelesítési protokollok blokkolásához
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 982c33c5c55e32ce48131a4b2f0911e4f23dd408
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98877288"
---
# <a name="conditional-access-block-legacy-authentication"></a>Feltételes hozzáférés: örökölt hitelesítés tiltása

Az örökölt hitelesítési protokollok fokozott kockázata miatt a Microsoft azt javasolja, hogy a szervezetek ezen protokollok használatával blokkolják a hitelesítési kérelmeket, és modern hitelesítést igényeljenek.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

A következő lépések segítséget nyújtanak egy feltételes hozzáférési szabályzat létrehozásához az örökölt hitelesítési kérelmek blokkolásához. Ezt a házirendet csak a [jelentések üzemmódba](howto-conditional-access-insights-reporting.md) helyezheti el, így a rendszergazdák meghatározhatják, hogy milyen hatással lesznek a meglévő felhasználókra. Ha a rendszergazdák kényelmesek, hogy a házirend a kívánt módon érvényes, **akkor a telepítésre vagy az** üzembe helyezésre adott csoportok hozzáadásával, illetve mások kizárásával válthatnak.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat** lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések** alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás** területen válassza a **minden felhasználó** lehetőséget.
   1. A **kizárás** területen válassza a **felhasználók és csoportok** lehetőséget, és válassza ki azokat a fiókokat, amelyeknek fenn kell tartaniuk a régi hitelesítés használatát. Zárja ki legalább egy fiókot, hogy elkerülje a zárolás kizárását. Ha nem zárja ki a fiókot, nem fogja tudni létrehozni ezt a házirendet.
   1. Válassza a **Kész** lehetőséget.
1. A **Cloud apps vagy műveletek** területen válassza a **minden felhőalapú alkalmazás** lehetőséget.
   1. Válassza a **Kész** lehetőséget.
1. A **feltételek**  >  **ügyfélalkalmazások** területen állítsa az  **Igen** értékre.
   1. Csak a dobozok **Exchange ActiveSync-ügyfelek** és **más ügyfelek** jelölőnégyzetének bejelölése.
   1. Válassza a **Kész** lehetőséget.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza a **hozzáférés letiltása** lehetőséget.
   1. Válassza a **Kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **házirendet** **csak jelentésre**.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-insights-reporting.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)

[Többfunkciós eszköz vagy alkalmazás beállítása az e-mailek küldéséhez Microsoft 365 használatával](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)
