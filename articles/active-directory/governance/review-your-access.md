---
title: Tekintse át a hozzáférést a csoportokhoz & alkalmazások hozzáférési felülvizsgálatokban – Azure AD
description: Megtudhatja, hogyan tekintheti át a csoportokhoz vagy alkalmazásokhoz való saját hozzáférését Azure Active Directory hozzáférési felülvizsgálatokban.
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
ms.openlocfilehash: b3fef2f85ca7e7b4034c8582477796d49446ea44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746779"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Az Azure AD hozzáférési felülvizsgálatokban lévő csoportok és alkalmazások hozzáférésének áttekintése

Azure Active Directory (Azure AD) leegyszerűsíti, hogy a vállalatok hogyan kezelhetik az Azure ad-ben és más Microsoft Online Servicesben lévő csoportokhoz vagy alkalmazásokhoz való hozzáférést az Azure AD hozzáférési felülvizsgálatok szolgáltatásával.

Ez a cikk bemutatja, hogyan tekintheti át a saját hozzáférését egy csoporthoz vagy alkalmazáshoz.

## <a name="review-your-access-using-my-apps"></a>Hozzáférés áttekintése saját alkalmazások használatával

A hozzáférési felülvizsgálat végrehajtásának első lépése a hozzáférési felülvizsgálat megkeresése és megnyitása.

>[!IMPORTANT]
> Az e-mailek fogadása késéssel jár, és néhány esetben akár 24 órát is igénybe vehet. Adja hozzá azure-noreply@microsoft.com a biztonságos címzettek listájához, és győződjön meg arról, hogy az összes e-mailt fogadja.

1. Keresse meg a Microsoft e-mail-címét, amely arra kéri, hogy tekintse át a hozzáférést. Íme egy példa e-mailben, amely áttekinti a csoporthoz való hozzáférést.

    ![Példa a Microsoft e-mail-címére a csoporthoz való hozzáférés áttekintéséhez](./media/review-your-access/access-review-email.png)

1. A hozzáférési felülvizsgálat megnyitásához kattintson a **hozzáférés áttekintése** hivatkozásra.

Ha nem rendelkezik az e-mail-címmel, a következő lépésekkel megkeresheti a függőben lévő hozzáférési felülvizsgálatokat.

1. Jelentkezzen be a saját alkalmazások portálján a következő címen: [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![A saját alkalmazások portál felsorolja azokat az alkalmazásokat, amelyekhez jogosultsággal rendelkezik](./media/review-your-access/myapps-access-panel.png)

1. Az oldal jobb felső sarkában kattintson a felhasználó szimbólumára. Ekkor megjelenik a neve és az alapértelmezett szervezete. Ha egynél több szervezet szerepel a listán, válassza ki azt a szervezetet, amely a hozzáférési felülvizsgálatot kérte.

1. Az oldal jobb oldalán kattintson a **hozzáférési felülvizsgálatok** csempére a függőben lévő hozzáférési felülvizsgálatok listájának megtekintéséhez.

    A csempe nem látható, ha nincsenek végrehajtandó hozzáférési felülvizsgálatok az adott szervezetre vonatkozóan, és ilyenkor semmilyen műveletre nincs szükség.

    ![Alkalmazások és csoportok függőben lévő hozzáférési felülvizsgálatok listája](./media/review-your-access/access-reviews-list.png)

1. Kattintson a **megkezdés felülvizsgálati** hivatkozásra a végrehajtani kívánt hozzáférési felülvizsgálathoz.

### <a name="perform-the-access-review"></a>A hozzáférési felülvizsgálat végrehajtása

Ha megnyitotta a hozzáférési felülvizsgálatot, a hozzáférését láthatja.

1. Tekintse át a hozzáférését, és döntse el, hogy van-e hozzáférése.

    Ha a kérelem a mások számára való hozzáférés felülvizsgálatát kéri, a lap eltérő lesz. További információ: a [csoportok vagy alkalmazások hozzáférésének áttekintése](perform-access-review.md).

    ![A megnyitott hozzáférési felülvizsgálatot bemutató képernyőkép, amely azt kérdezi, hogy továbbra is szüksége van-e egy csoporthoz való hozzáférésre.](./media/review-your-access/perform-access-review.png)

1. Az **Igen** gombra kattintva megtarthatja a hozzáférését, vagy a **nem** gombra kattintva eltávolíthatja a hozzáférését.

1. Ha az **Igen** gombra kattint, lehet, hogy meg kell adnia egy indoklást az **OK** mezőben.

    ![Képernyőkép, amely egy befejezett hozzáférési felülvizsgálatot mutat be, amely megkérdezi, hogy továbbra is szüksége van-e egy csoport elérésére, az "igen" beállítással.](./media/review-your-access/perform-access-review-submit.png)

1. Kattintson a **Submit (Küldés**) gombra.

    A rendszer elküldte a kijelölést, és visszatér a saját alkalmazások portálra.

    Ha módosítani szeretné a választ, nyissa meg újra a hozzáférési felülvizsgálatok lapot, és frissítse a választ. Bármikor módosíthatja a választ, amíg a hozzáférési felülvizsgálat véget nem ér.

    > [!NOTE]
    > Ha azt jelezte, hogy már nincs szüksége a hozzáférésre, nem távolítja el azonnal. Ha a felülvizsgálat véget ért, vagy ha a rendszergazda leállítja a felülvizsgálatot, a rendszer eltávolítja.

## <a name="review-your-own-access-using-my-access-new"></a>Saját hozzáférés áttekintése a saját hozzáférés használatával (új)

Az új felhasználói felületet a saját hozzáférés a frissített felhasználói felülettel is kipróbálhatja:

### <a name="my-apps-portal"></a>Saját alkalmazások portál

1. Jelentkezzen be a saját alkalmazások portálján a következő címen: [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![A saját alkalmazások portál felsorolja azokat az alkalmazásokat, amelyekhez jogosultsággal rendelkezik](./media/review-your-access/myapps-access-panel.png)

2. Kattintson a **hozzáférési felülvizsgálatok** csempére a függőben lévő hozzáférési felülvizsgálatok listájának megtekintéséhez.

    > [!NOTE]
    > Ha a **hozzáférési felülvizsgálatok** csempe nem látható, akkor a szervezet számára nem végeznek hozzáférési felülvizsgálatokat, és jelenleg nincs szükség beavatkozásra.

3. Kattintson a **kipróbálás gombra!** az oldal tetején található szalagcímben az új saját hozzáférési felületre ugráshoz.

    ![Alkalmazások és csoportok függőben lévő hozzáférési felülvizsgálatok listája az előzetes verzióban megjelenített új felhasználói felülettel](./media/review-your-access/banner-your-access.png)

4. Folytatás a **hozzáférési felülvizsgálat végrehajtása** szakaszban

### <a name="email"></a>E-mail

>[!IMPORTANT]
> Az e-mailek fogadása késéssel jár, és néhány esetben akár 24 órát is igénybe vehet. Adja hozzá azure-noreply@microsoft.com a biztonságos címzettek listájához, és győződjön meg arról, hogy az összes e-mailt fogadja.

1. Keressen egy e-mailt a Microsofttól, és kérje meg, hogy tekintse át a hozzáférést. Az alábbi e-mail-üzenetet láthatja:

 ![Példa a Microsoft e-mail-címére a csoportokhoz való hozzáférés áttekintéséhez](./media/review-your-access/access-review-email-preview.png)

2. A hozzáférési felülvizsgálat megnyitásához kattintson a **hozzáférés áttekintése** hivatkozásra.

3. Folytatás a **hozzáférési felülvizsgálat végrehajtása** szakaszban

>[!NOTE]
>Ha a Start Review (indítás) gombra kattint, a **saját alkalmazások** elemre kattintva követheti a **saját alkalmazások portál** című részében ismertetett lépéseket.

### <a name="directly-at-my-access"></a>Közvetlenül a saját hozzáférés

A függőben lévő hozzáférési felülvizsgálatokat a böngésző használatával is megtekintheti a saját hozzáférés megnyitásához.

1. Bejelentkezés a My Access on https://myaccess.microsoft.com/

2. A bal oldali sávban található menüben válassza a **hozzáférési felülvizsgálatok** lehetőséget, hogy megtekintse a függőben lévő hozzáférési felülvizsgálatok listáját.

   ![hozzáférési felülvizsgálatok a menüben](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>A hozzáférési felülvizsgálat végrehajtása

1. A csoportok és alkalmazások területen a következőket láthatja:
    
    - **Név** A hozzáférési felülvizsgálat neve.
    - **Esedékes** A felülvizsgálat határideje. Ezt a dátumot követően a felhasználókat el lehet távolítani az áttekintett csoportból vagy alkalmazásból.
    - **Erőforrás** Az erőforrás neve a felülvizsgálat alatt.
    - **Folyamat állapota** Azon felhasználók száma, akik a hozzáférési felülvizsgálat összes felhasználójának számát áttekintették.
    
2. A kezdéshez kattintson a hozzáférési felülvizsgálat nevére.

   ![Alkalmazások és csoportok függőben lévő hozzáférési felülvizsgálatok listája](./media/review-your-access/access-reviews-list-preview.png)

3. Tekintse át a hozzáférését, és döntse el, hogy van-e hozzáférése.

    Ha a kérelem a mások számára való hozzáférés felülvizsgálatát kéri, a lap eltérő lesz. További információ: a [csoportok vagy alkalmazások hozzáférésének áttekintése](perform-access-review.md).

    ![Nyissa meg a hozzáférési felülvizsgálatot, és kérje meg, hogy van-e hozzáférése egy csoporthoz](./media/review-your-access/review-access-preview.png)

1. Válassza az **Igen** lehetőséget a hozzáférésének megtartásához, vagy válassza a **nem** lehetőséget a hozzáférés eltávolításához.

1. Ha az **Igen** gombra kattint, lehet, hogy meg kell adnia egy indoklást az **OK** mezőben.

    ![A hozzáférési felülvizsgálat befejezésével megkérdezte, hogy továbbra is szüksége van-e egy csoport elérésére](./media/review-your-access/review-access-yes-preview.png)

1. Kattintson a **Submit (Küldés**) gombra.

    A rendszer elküldte a kijelölést, és visszatér a saját hozzáférés lapra.

    Ha módosítani szeretné a választ, nyissa meg újra a hozzáférési felülvizsgálatok lapot, és frissítse a választ. Bármikor módosíthatja a választ, amíg a hozzáférési felülvizsgálat véget nem ér.

    > [!NOTE]
    > Ha azt jelezte, hogy már nincs szüksége a hozzáférésre, nem távolítja el azonnal. Ha a felülvizsgálat véget ért, vagy ha a rendszergazda leállítja a felülvizsgálatot, a rendszer eltávolítja.

## <a name="next-steps"></a>Következő lépések

- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md)
