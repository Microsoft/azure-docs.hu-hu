---
title: A szervezet hozzáférésének kezelése az alkalmazásokhoz & csoportokhoz – Azure AD
description: Megtudhatja, hogyan hajthat végre hozzáférési felülvizsgálatot a szervezet alkalmazásaihoz és csoportjaihoz tartozó biztonsági hozzáférés kezeléséhez a saját alkalmazások portálon.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 34885e2a364778a2f81f4920aa26aa3bb5f40320
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100095018"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Hozzáférési felülvizsgálat végrehajtása a saját alkalmazások portálján

Használhatja munkahelyi vagy iskolai fiókját a Web-based **My apps** portálon az alkalmazások és csoportok hozzáférési felülvizsgálatának elvégzéséhez. A hozzáférési felülvizsgálatok segítségével kezelheti az elavult hozzáféréseket, illetve módosíthatja a hozzáférési követelményeket, és ellenőrizheti és frissítheti azokat.

Ha nem rendelkezik hozzáféréssel a **saját alkalmazások** portálhoz, kérjen engedélyt az ügyfélszolgálattól.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ez a tartalom a **saját alkalmazások** felhasználói számára készült. Ha Ön rendszergazda, további információt talál arról, hogyan állíthatja be és kezelheti felhőalapú alkalmazásait az [alkalmazás-felügyeleti dokumentációban](../manage-apps/index.yml).
>
> Ha személyes Microsoft-fiók használatával jelentkezik be, akkor továbbra is bejelentkezhet a szervezet tartománynevére (például contoso.com) vagy a szervezet **bérlői azonosítójára** a rendszergazdától a következő URL-címek egyikében:
>
>   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
>   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

## <a name="manage-access-reviews"></a>Hozzáférési felülvizsgálatok kezelése

Ha a rendszergazda engedélyezte a saját hozzáférési felülvizsgálatok elvégzését, a saját **alkalmazások** portál lapon a **hozzáférési felülvizsgálatok** csempén kezelheti a csoportokat vagy az alkalmazásokat.

>[!Note]
>Ha nem látja a **hozzáférési felülvizsgálatok** csempét, akkor az azt jelenti, hogy nincs engedélye a hozzáférési felülvizsgálatok elvégzésére, vagy ha nincs függőben lévő felülvizsgálat, amely a jóváhagyásra vár. Ha úgy gondolja, hogy hozzáféréssel kell rendelkeznie a csempéhez, segítségért forduljon az ügyfélszolgálathoz.

## <a name="to-perform-your-access-reviews"></a>A hozzáférési felülvizsgálatok végrehajtása

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjába.

1. Nyissa meg a webböngészőjét, és lépjen a webhelyre https://myapps.microsoft.com , vagy használja a szervezete által biztosított hivatkozást. Előfordulhat például, hogy a szervezete testreszabott lapjára irányítja át, például: https://myapps.microsoft.com/contoso.com .

    Megjelenik az **alkalmazások** lap, amely megjeleníti a szervezete tulajdonában lévő összes felhőalapú alkalmazást, és elérhető a használatra.

    ![Alkalmazások lap a saját alkalmazások portálon](media/my-apps-portal/my-apps-home.png)

1. Válassza a **hozzáférési felülvizsgálatok** csempét a jóváhagyásra váró hozzáférési felülvizsgálatok listájának megtekintéséhez.

    ![Hozzáférési felülvizsgálatok lap a szervezetre vonatkozó függőben lévő hozzáférési felülvizsgálatokkal](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

1. A hozzáférési felülvizsgálat elindításához válassza az **Áttekintés megkezdése** lehetőséget.

5. Tekintse át a hozzáférését, és döntse el, hogy továbbra is szükséges-e.

    ![Hozzáférési felülvizsgálati oldal, amely az Áttekintés részleteit jeleníti meg](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Ha Ön rendszergazda, és lehetővé teszi, hogy áttekintse a szervezet hozzáférését a csoportokhoz és alkalmazásokhoz, egy másik oldal jelenik meg. A csoportok és alkalmazások szervezethez való áttekintésével kapcsolatos további információkért lásd: [hozzáférés ellenőrzése csoportokhoz vagy alkalmazásokhoz az Azure ad hozzáférési felülvizsgálatokban](../governance/perform-access-review.md).

6. Válassza az **Igen** lehetőséget a hozzáférésének megtartásához, vagy a **nem** gombra a hozzáférés eltávolításához.

    Ha az **Igen** lehetőséget választja, lehet, hogy meg kell adnia egy indoklást az **OK** mezőben.

    ![Hozzáférési felülvizsgálati oldal, amely az OK mezőt mutatja a minta szöveggel](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Válassza a **Küldés** lehetőséget.

    A hozzáférési felülvizsgálat elkészült, és visszatér a **saját alkalmazások** portálra.

    >[!Note]
    >A hozzáférését bármikor módosíthatja, amíg a hozzáférési felülvizsgálati időszak véget nem ér. Ha eltávolít egy alkalmazáshoz vagy csoporthoz való hozzáférését, azt nem távolítja el azonnal. Az eltávolítás akkor történik meg, amikor a hozzáférési felülvizsgálati időszak véget ér, vagy amikor a rendszergazda bezárja a felülvizsgálatot.

## <a name="next-steps"></a>Következő lépések

- [Alkalmazások elérése és használata a saját alkalmazások portálján](my-apps-portal-end-user-access.md)
- [Profilinformációk módosítása](./my-account-portal-settings.md)
- [A csoportok kapcsolódó információinak megtekintése és frissítése](my-apps-portal-end-user-groups.md)