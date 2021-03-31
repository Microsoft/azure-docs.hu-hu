---
title: Konfigurálja a "Stay bejelentkezve?" Azure Active Directory-fiókok kérése
description: További információ a bejelentkezett (KMSI) szolgáltatásról, amely megjeleníti a bejelentkezett maradni? Rákérdezés, hogyan konfigurálható a Azure Active Directory portálon, és hogyan lehet elhárítani a bejelentkezési problémákat.
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed6bc43dfc15abf2bdf9f38a5de2240d348d6fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89320256"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>Konfigurálja a "Stay bejelentkezve?" Azure AD-fiókok bekérése

A bejelentkezett üzenetek (KMSI) megjeleníti a **bejelentkezett tartózkodást?** Rákérdezés arra, hogy a felhasználó sikeresen bejelentkezik. Ha a felhasználó az **Igen** értékre válaszol erre a kérdésre, a bejelentkezett üzenetek megőrzése szolgáltatás állandó [frissítési jogkivonatot](../develop/developer-glossary.md#refresh-token)ad nekik. Összevont bérlők esetén a parancssor azt mutatja be, hogy a felhasználó sikeresen elvégezte a hitelesítést az összevont identitás-szolgáltatással.

A következő ábrán látható a felügyelt bérlő és az összevont bérlő felhasználói bejelentkezési folyamata, valamint az új bejelentkezett üzenet. Ez a folyamat intelligens logikát tartalmaz, így a **bejelentkezett marad?** beállítás nem jelenik meg, ha a gépi tanulási rendszer magas kockázatú bejelentkezést vagy egy megosztott eszközről való bejelentkezést észlel.

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="A felügyelt vagy összevont bérlő felhasználói bejelentkezési folyamatát bemutató ábra":::

> [!NOTE]
> A bejelentkezett adatok megtartása beállítás megadásához Azure Active Directory (Azure AD) prémium 1, prémium 2 vagy alapszintű kiadást kell használnia, vagy Microsoft 365 licencre van szükség. További információ a licencelésről és a kiadásokról: [regisztráció a prémium szintű Azure ADra](active-directory-get-started-premium.md).<br><br>A prémium szintű Azure AD és az alapszintű kiadás az Azure AD világméretű példányával érhető el Kínában lévő ügyfelek számára. Az Azure AD Prémium és Alapszintű kiadása jelenleg nem támogatott Kínában a 21Vianet által működtetett Azure-szolgáltatásban. További információért forduljon hozzánk az [Azure ad-fórum](https://feedback.azure.com/forums/169401-azure-active-directory/)használatával.

## <a name="configure-kmsi"></a>KMSI konfigurálása

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).
1. Válassza a **Azure Active Directory** lehetőséget, válassza a **vállalati védjegyezés** lehetőséget, majd válassza a **Konfigurálás** lehetőséget.
1. A **Speciális beállítások** szakaszban keresse meg a show (megjelenítés) lehetőséget, hogy a beállítás **bejelentkezve maradjon** .

   Ezzel a beállítással kiválaszthatja, hogy a felhasználók továbbra is bejelentkeznek-e az Azure AD-ba, amíg a kijelentkezés nem történik meg.
   * Ha a **nem** lehetőséget választja, a **bejelentkezett marad?** beállítás rejtett, miután a felhasználó sikeresen bejelentkezik, és a felhasználónak minden alkalommal be kell jelentkeznie, amikor a böngésző be van zárva, és újra megnyílik.
   * Ha az **Igen** lehetőséget választja, a **bejelentkezett marad?** lehetőség jelenik meg a felhasználó számára.

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="Képernyőfelvétel: a megjelenítési lehetőség, hogy továbbra is be legyen jelentkezve beállítás":::

## <a name="troubleshoot-sign-in-issues"></a>Bejelentkezési problémák elhárítása

Ha a felhasználó nem a **bejelentkezett marad?** üzenet jelenik meg, ahogy az a következő ábrán is látható, de a bejelentkezési kísérlet megszűnik, egy bejelentkezési naplóbejegyzés jelenik meg, amely jelzi a megszakítást.

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="Megjeleníti a bejelentkezett maradni? gyors":::

A bejelentkezési hibával kapcsolatos részletek a következőképpen jelennek meg, és a példában vannak kiemelve.

* **Bejelentkezési hibakód**: 50140
* **Hiba oka**: Ez a hiba a felhasználó bejelentkezésekor a "Bejelentkezés megtartása" megszakítás miatt történt.

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="Példa bejelentkezési naplóbejegyzést a Keep Me bejelentkezett megszakítással":::

Leállíthatja a felhasználókat, hogy meglássák a megszakítást úgy, **hogy a Megjelenítés lehetőséget** úgy állítja be, hogy a **nem** értékre legyen állítva a speciális védjegyezési beállításokban. Ez letiltja az Azure AD-címtár összes felhasználójának KMSI-kérését.

A feltételes hozzáférés állandó böngésző-munkamenet vezérlőelemeit is használhatja annak megakadályozása érdekében, hogy a felhasználók láthatják a KMSI-kérést. Ez a beállítás lehetővé teszi, hogy letiltsa a KMSI-kérést a felhasználók (például a globális rendszergazdák) felhasználói csoportja számára, anélkül, hogy ez befolyásolná a címtárban található többi felhasználó bejelentkezési viselkedését. További információ: [felhasználói bejelentkezés gyakorisága](../conditional-access/howto-conditional-access-session-lifetime.md). 

Annak biztosítása érdekében, hogy a KMSI-kérés csak akkor jelenjen meg, ha a felhasználó számára hasznos, a KMSI-kérés szándékosan nem jelenik meg a következő helyzetekben:

* A felhasználó zökkenőmentes SSO és integrált Windows-hitelesítés (IWA) használatával jelentkezik be
* A felhasználó Active Directory összevonási szolgáltatások (AD FS)on és IWA keresztül jelentkezik be
* A felhasználó a bérlő egyik vendége
* A felhasználó kockázati pontszáma magas
* Bejelentkezés a felhasználó vagy a rendszergazda beleegyező folyamata során történik
* Az állandó böngésző munkamenet-vezérlése feltételes hozzáférési szabályzatban van konfigurálva

## <a name="next-steps"></a>Következő lépések

További információ a bejelentkezési munkamenet időtúllépését érintő egyéb beállításokról:

* Microsoft 365 – [üresjárati munkamenet időkorlátja](/sharepoint/sign-out-inactive-users)
* Azure AD feltételes hozzáférés – [felhasználói bejelentkezés gyakorisága](../conditional-access/howto-conditional-access-session-lifetime.md)
* Azure Portal – a [címtár szintű inaktivitás időkorlátja](../../azure-portal/set-preferences.md#change-the-directory-timeout-setting-admin)