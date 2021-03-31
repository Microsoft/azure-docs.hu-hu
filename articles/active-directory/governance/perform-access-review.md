---
title: Tekintse át a csoportokhoz való hozzáférést a hozzáférési felülvizsgálatok & alkalmazásaiban – Azure AD
description: Megtudhatja, hogyan tekintheti át a csoporttagok vagy az alkalmazások hozzáférését Azure Active Directory hozzáférési felülvizsgálatokban.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5cd7a5737b2d13e63eabbbddd076cfc7aff83ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746796"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Csoportok és alkalmazások hozzáférésének áttekintése az Azure AD hozzáférési felülvizsgálatokban

A Azure Active Directory (Azure AD) leegyszerűsíti, hogy a vállalatok hogyan kezelhetik az Azure AD-ben és más Microsoft Online Servicesben lévő csoportokhoz és alkalmazásokhoz való hozzáférést az Azure AD hozzáférési felülvizsgálatok szolgáltatásával. Ez a cikk azt mutatja be, hogy a kijelölt véleményező hogyan hajt végre hozzáférési felülvizsgálatot egy csoport vagy egy alkalmazáshoz hozzáféréssel rendelkező felhasználók tagjai számára. Ha szeretné áttekinteni a csomagokat, tekintse át a hozzáférési csomag elérését az [Azure ad-jogosultságok kezelése](entitlement-management-access-reviews-review-access.md) szolgáltatásban

## <a name="perform-access-review-using-my-apps"></a>Hozzáférési felülvizsgálat végrehajtása a saját alkalmazások használatával

A hozzáférési felülvizsgálati folyamat elindításához az értesítő e-mailben vagy közvetlenül a webhelyhez juthat.

- **E-mail cím**:

>[!IMPORTANT]
> Az e-mailek fogadása késéssel jár, és néhány esetben akár 24 órát is igénybe vehet. Adja hozzá azure-noreply@microsoft.com a biztonságos címzettek listájához, és győződjön meg arról, hogy az összes e-mailt fogadja.

1. Keressen egy e-mailt a Microsofttól, és kérje meg, hogy tekintse át a hozzáférést. Íme egy példa e-mailben a csoport hozzáférésének áttekintéséhez.

    ![Képernyőkép, amely a Microsoft egy adott e-mail-címét jeleníti meg a csoportokhoz való hozzáférés áttekintéséhez.](./media/perform-access-review/access-review-email.png)

1. A hozzáférési felülvizsgálat megnyitásához kattintson a **Start Review (áttekintés** ) hivatkozásra.

- **Ha nem rendelkezik az e-mail-címmel**, a következő lépésekkel megkeresheti a függőben lévő hozzáférési felülvizsgálatokat.

    1. Jelentkezzen be a saját alkalmazások portálján a következő címen: [https://myapps.microsoft.com](https://myapps.microsoft.com) .

        ![A saját alkalmazások portál felsorolja azokat az alkalmazásokat, amelyekhez jogosultsággal rendelkezik](./media/perform-access-review/myapps-access-panel.png)

    1. A lap jobb felső sarkában kattintson a név és az alapértelmezett szervezet melletti felhasználóra. Ha egynél több szervezet szerepel a listán, válassza ki azt a szervezetet, amely a hozzáférési felülvizsgálatot kérte.

    1. Kattintson a **hozzáférési felülvizsgálatok** csempére a függőben lévő hozzáférési felülvizsgálatok listájának megtekintéséhez.

        > [!NOTE]
        > Ha a **hozzáférési felülvizsgálatok** csempe nem látható, akkor a szervezet számára nem végeznek hozzáférési felülvizsgálatokat, és jelenleg nincs szükség beavatkozásra.

        ![Képernyőkép, amely megjeleníti az alkalmazások és csoportok függőben lévő hozzáférési felülvizsgálatok listáját.](./media/perform-access-review/access-reviews-list.png)

    1. Kattintson a **megkezdés felülvizsgálati** hivatkozásra a végrehajtani kívánt hozzáférési felülvizsgálathoz.

Miután megnyitotta a hozzáférési felülvizsgálatot, megtekintheti azoknak a felhasználóknak a nevét, akiknek hozzáférését át kell tekinteni.

Ha a kérést a saját hozzáférésének felülvizsgálatára kéri, a lap eltérő lesz. További információkért lásd: [hozzáférés áttekintése csoportokhoz vagy alkalmazásokhoz](review-your-access.md).

![A felülvizsgálati felhasználókat felsoroló hozzáférési felülvizsgálat megnyitása](./media/perform-access-review/perform-access-review.png)

Két módon engedélyezheti vagy tilthatja le a hozzáférést:

- Egy vagy több felhasználó manuálisan is jóváhagyhatja vagy megtagadhatja a hozzáférést az egyes felhasználói kérések megfelelő műveletének kiválasztásával.
- A rendszerjavaslatok elfogadására is lehetőség van.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Egy vagy több felhasználó hozzáférésének jóváhagyása vagy megtagadása

1. Tekintse át a felhasználók listáját, és döntse el, hogy jóváhagyja vagy megtagadja a folyamatos hozzáférést.

    - Egyetlen felhasználó hozzáférésének jóváhagyásához vagy megtagadásához kattintson a sorra egy ablak megnyitásához a végrehajtandó művelet megadásához. 
    - Több felhasználó hozzáférésének jóváhagyásához vagy megtagadásához vegyen fel ellenőrző jeleket a felhasználók mellett, majd kattintson az **X felhasználó ellenőrzése** gombra egy ablak megnyitásához a végrehajtandó művelet megadásához.

1. Kattintson a **jóváhagyás** vagy a **Megtagadás** gombra. 

    ![A jóváhagyást, megtagadást és nem ismerő beállításokat tartalmazó művelet ablaka](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > Ha nem biztos benne, akkor kattintson a **nem tudom** gombra. a felhasználó pedig megtartja a hozzáférését, és az Ön választása a naplókban van rögzítve.

1. Előfordulhat, hogy a hozzáférési felülvizsgálat rendszergazdája megkövetelheti, hogy adjon meg egy **okot az OK mezőben a** döntéshez. Még akkor is, ha nincs szükség okra. Továbbra is megadhatja a döntés okát, és a benne foglalt információk elérhetők lesznek más felülvizsgálók számára.

1. Miután megadta a végrehajtandó műveletet, kattintson a **Mentés** gombra.

    >[!NOTE]
    > Bármikor módosíthatja a választ, mielőtt a hozzáférési felülvizsgálat véget ér. Ha módosítani szeretné a választ, válassza ki a sort, és frissítse a választ. Jóváhagyhat például egy korábban megtagadott felhasználót, vagy megtagadhatja a korábban jóváhagyott felhasználókat.

    >[!IMPORTANT]
    > - Ha a felhasználó hozzáférése megtagadva, a rendszer nem távolítja el azonnal. Ha a felülvizsgálati időszak véget ér, vagy amikor a rendszergazda leállítja a felülvizsgálatot, a rendszer eltávolítja azokat, ha engedélyezve van az [automatikus alkalmazás](complete-access-review.md#apply-the-changes) .
    > - Ha több felülvizsgáló is van, a rendszer rögzíti a legutóbbi elküldött választ. Vegyünk például egy olyan példát, amelyben a rendszergazda két véleményezőt jelöl ki: Alice és Bob. Alice először megnyitja a hozzáférési felülvizsgálatot, és jóváhagyja a felhasználó hozzáférési kérelmét. A felülvizsgálati időszak lejárta előtt Bob megnyitja a hozzáférési felülvizsgálatot, és megtagadja a hozzáférést az Alice által korábban jóváhagyott kérelemhez. Az utolsó döntés, amely megtagadja a hozzáférést, a rendszer rögzíti a választ.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Hozzáférés jóváhagyása vagy elutasítása javaslatok alapján

Ahhoz, hogy a hozzáférési felülvizsgálatok könnyebben és gyorsabban elérhetők legyenek, javaslatokat is biztosítunk, amelyek egyetlen kattintással elfogadhatók. A javaslatok a felhasználó bejelentkezési tevékenysége alapján jönnek létre.

1. A lap alján található kék sávban kattintson a **javaslatok elfogadása** elemre.

    ![Képernyőkép: a megnyitási javaslatok kiválasztása gomb kiválasztásával megjelenítheti az Open Access felülvizsgálati listáját.](./media/perform-access-review/accept-recommendations.png)

    Ekkor megjelenik a javasolt műveletek összegzése.

    ![A javasolt műveletek összegzését megjelenítő ablak](./media/perform-access-review/accept-recommendations-summary.png)

1. A javaslatok elfogadásához kattintson **az OK** gombra.

## <a name="perform-access-review-using-my-access-new"></a>Hozzáférési felülvizsgálat végrehajtása a saját hozzáférés használatával (új)

Az új véleményezői élményt a saját hozzáférés frissített felhasználói felületével érheti el pár különböző módon:

### <a name="my-apps-portal"></a>Saját alkalmazások portál

1. Jelentkezzen be az alkalmazásaiba a következő címen: [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![A saját alkalmazások portál felsorolja azokat az alkalmazásokat, amelyekhez jogosultsággal rendelkezik](./media/perform-access-review/myapps-access-panel.png)

2. Kattintson a **hozzáférési felülvizsgálatok** csempére a függőben lévő hozzáférési felülvizsgálatok listájának megtekintéséhez.

    > [!NOTE]
    > Ha a **hozzáférési felülvizsgálatok** csempe nem látható, akkor a szervezet számára nem végeznek hozzáférési felülvizsgálatokat, és jelenleg nincs szükség beavatkozásra.

![Alkalmazások és csoportok függőben lévő hozzáférési felülvizsgálatok listája az előzetes verzióban megjelenített új felhasználói felülettel](./media/perform-access-review/banner.png)

3. Kattintson a **kipróbálás gombra!** a lap tetején található szalagcímben. Ekkor megnyílik az új saját hozzáférési élmény.
  
### <a name="email"></a>E-mail

  >[!IMPORTANT]
> Az e-mailek fogadása késéssel jár, és néhány esetben akár 24 órát is igénybe vehet. Adja hozzá azure-noreply@microsoft.com a biztonságos címzettek listájához, és győződjön meg arról, hogy az összes e-mailt fogadja.

   1. Keressen egy e-mailt a Microsofttól, és kérje meg, hogy tekintse át a hozzáférést. Az alábbi e-mail-üzenetet láthatja:

   ![Példa a Microsoft e-mail-címére a csoportokhoz való hozzáférés áttekintéséhez](./media/perform-access-review/access-review-email-preview.png)

   2. A hozzáférési felülvizsgálat megnyitásához kattintson a **Start Review (áttekintés** ) hivatkozásra.

>[!NOTE]
>Ha a Start Review (indítás) gombra kattint, a **saját alkalmazások** elemre kattintva követheti a **saját alkalmazások portál** című részében ismertetett lépéseket.

### <a name="navigate-to-my-access-directly"></a>Közvetlen hozzáférés

A függőben lévő hozzáférési felülvizsgálatokat a böngésző használatával is megtekintheti a saját hozzáférés megnyitásához.

1. Bejelentkezés a My Access on https://myaccess.microsoft.com/

2. A bal oldali sávban található menüben válassza a **hozzáférési felülvizsgálatok** lehetőséget, hogy megtekintse a függőben lévő hozzáférési felülvizsgálatok listáját.

   ![hozzáférési felülvizsgálatok a menüben](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Egy vagy több felhasználó hozzáférésének jóváhagyása vagy megtagadása

Miután megnyitotta a saját hozzáférését a csoportok és alkalmazások területen, láthatja a következőket:

- **Név** A hozzáférési felülvizsgálat neve.
- **Esedékes** A felülvizsgálat határideje. Ezt a dátumot követően a felhasználókat el lehet távolítani az áttekintett csoportból vagy alkalmazásból.
- **Erőforrás** Az erőforrás neve a felülvizsgálat alatt.
- **Folyamat állapota** Azon felhasználók száma, akik a hozzáférési felülvizsgálat összes felhasználójának számát áttekintették.

A kezdéshez kattintson a hozzáférési felülvizsgálat nevére.

![Alkalmazások és csoportok függőben lévő hozzáférési felülvizsgálatok listája](./media/perform-access-review/access-reviews-list-preview.png)

A megnyitását követően megjelenik a hozzáférési felülvizsgálat hatókörében lévő felhasználók listája. Ha a kérést a saját hozzáférésének felülvizsgálatára kéri, a lap eltérő lesz. További információkért lásd: [hozzáférés áttekintése csoportokhoz vagy alkalmazásokhoz](review-your-access.md).

Két módon engedélyezheti vagy tilthatja le a hozzáférést:

- Egy vagy több felhasználóhoz manuálisan is jóváhagyhatja vagy megtagadhatja a hozzáférést.
- A rendszerjavaslatok elfogadására is lehetőség van.

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Egy vagy több felhasználó hozzáférésének manuális jóváhagyása vagy megtagadása

1. Tekintse át a felhasználók listáját, és döntse el, hogy jóváhagyja vagy megtagadja a folyamatos hozzáférést.
2. Válasszon ki egy vagy több felhasználót a nevük melletti körre kattintva.
3. Válassza a fenti sáv **jóváhagyás** vagy **Megtagadás** elemét.
    - Ha nem biztos benne, akkor kattintson a **nem tudom** gombra. A felhasználónak meg kell őriznie a hozzáférését, és az Ön választása a naplókban van rögzítve. Fontos, hogy ne feledje, hogy az Ön által megadott bármilyen információ elérhető lesz a többi felülvizsgáló számára. Elolvashatják a megjegyzéseit, és figyelembe veszik azokat, amikor áttekintik a kérést.

    ![Nyissa meg a hozzáférési felülvizsgálatot, amely felsorolja a felülvizsgálatot igénylő felhasználókat](./media/perform-access-review/user-list-preview.png)

4. Előfordulhat, hogy a hozzáférési felülvizsgálat rendszergazdája megkövetelheti, hogy adjon meg egy **okot az OK mezőben a** döntéshez. Még akkor is, ha nincs szükség okra. Továbbra is megadhatja a döntés okát, és a benne foglalt információk a többi jóváhagyó számára elérhetők lesznek a felülvizsgálathoz.

5. Kattintson a **Submit (Küldés**) gombra.
    - Bármikor módosíthatja a választ, amíg a hozzáférési felülvizsgálat véget nem ér. Ha módosítani szeretné a választ, válassza ki a sort, és frissítse a választ. Jóváhagyhat például egy korábban megtagadott felhasználót, vagy megtagadhatja a korábban jóváhagyott felhasználókat.

 >[!IMPORTANT]
 > - Ha a felhasználó hozzáférése megtagadva, a rendszer nem távolítja el azonnal. Ha a felülvizsgálati időszak véget ér, vagy amikor a rendszergazda leállítja a felülvizsgálatot, a rendszer eltávolítja azokat. 
 > - Ha több felülvizsgáló is van, a rendszer rögzíti a legutóbbi elküldött választ. Vegyünk például egy olyan példát, amelyben a rendszergazda két véleményezőt jelöl ki: Alice és Bob. Alice először megnyitja a hozzáférési felülvizsgálatot, és jóváhagyja a felhasználó hozzáférési kérelmét. A felülvizsgálati időszak lejárta előtt Bob megnyitja a hozzáférési felülvizsgálatot, és megtagadja a hozzáférést az Alice által korábban jóváhagyott kérelemhez. Az utolsó döntés, amely megtagadja a hozzáférést, a rendszer rögzíti a választ.

#### <a name="approve-or-deny-access-based-on-recommendations"></a>Hozzáférés jóváhagyása vagy elutasítása javaslatok alapján

Ahhoz, hogy a hozzáférési felülvizsgálatok könnyebben és gyorsabban elérhetők legyenek, javaslatokat is biztosítunk, amelyek egyetlen kattintással elfogadhatók. A javaslatok a felhasználó bejelentkezési tevékenysége alapján jönnek létre.

1. Válasszon ki egy vagy több felhasználót, majd kattintson a **javaslatok elfogadása** elemre.

    ![Hozzáférés-felülvizsgálati lista megnyitása a javaslatok elfogadása gomb megjelenítéséhez](./media/perform-access-review/accept-recommendations-preview.png)

1. A javaslatok elfogadásához kattintson a **Submit (Küldés** ) gombra.

Az összes felhasználóra vonatkozó javaslatok elfogadásához győződjön meg arról, hogy senki sincs kiválasztva, majd kattintson a felső sávon a **javaslatok elfogadása** gombra.

>[!NOTE]
>A javaslatok elfogadását követően a korábbi döntések nem változnak.

## <a name="next-steps"></a>Következő lépések

- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md)
