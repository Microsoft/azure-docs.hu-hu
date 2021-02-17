---
title: Oktatóanyag – erőforrásokhoz való hozzáférés kezelése az Azure AD-jogosultságok kezelésében
description: Lépésenkénti útmutató az első hozzáférési csomag létrehozásához a Azure Active Directory jogosultságok kezelése Azure Portal használatával.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 09/30/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1adbc653c8b698cb7b439b54b0a77d2b8cd4042a
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547400"
---
# <a name="tutorial-manage-access-to-resources-in-azure-ad-entitlement-management"></a>Oktatóanyag: az erőforrásokhoz való hozzáférés kezelése az Azure AD-jogosultságok kezelésében

A szervezetek számára fontos funkció az összes erőforráshoz, például a csoportokhoz, alkalmazásokhoz és webhelyekhez való hozzáférés kezelése. Azt szeretné, hogy az alkalmazottak a megfelelő szintű hozzáférést biztosítsanak a hatékonysághoz, és el kell távolítaniuk a hozzáférést, ha már nincs rá szükség.

Ebben az oktatóanyagban a Woodgrove Bank rendszergazdaként működik. A rendszer arra kérte, hogy hozzon létre egy olyan erőforrás-csomagot, amelyet a belső felhasználók használhatnak az önkiszolgáló kéréshez. A kérések nem igényelnek jóváhagyást, és a felhasználó hozzáférése 30 nap után lejár. Ebben az oktatóanyagban a marketing kampány erőforrásai csak egyetlen csoport tagjai, de lehetnek csoportok, alkalmazások vagy SharePoint Online-webhelyek gyűjteményei.

![A forgatókönyv áttekintését bemutató diagram.](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférési csomag létrehozása erőforrásként egy csoporttal
> * Hozzáférés kérésének engedélyezése a címtárban lévő felhasználónak
> * Bemutatjuk, hogyan kérheti a belső felhasználó a hozzáférési csomagot

Az Azure Active Directory jogosultságok felügyeletének üzembe helyezési folyamatának lépésenkénti bemutatásához, beleértve az első hozzáférési csomag létrehozását is, tekintse meg a következő videót:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-jogosultságok felügyeletének használatához a következő licencek egyike szükséges:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5 licenc

További információkért lásd a [licencekre vonatkozó követelményeket](entitlement-management-overview.md#license-requirements).

## <a name="step-1-set-up-users-and-group"></a>1. lépés: felhasználók és csoportok beállítása

Egy erőforrás-címtárhoz egy vagy több megosztani kívánt erőforrás tartozik. Ebben a lépésben létrehoz egy **marketing-erőforrások** nevű csoportot a Woodgrove Bank címtárában, amely a jogosultságok kezelésének célként szolgáló erőforrása. A belső kérelmezőt is be kell állítania.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

![Felhasználók és csoportok létrehozása](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként vagy felhasználói rendszergazdaként.  

1. A bal oldali navigációs sávon kattintson a **Azure Active Directory** elemre.

1. Hozza létre vagy konfigurálja a következő két felhasználót. Ezeket a neveket vagy más neveket is használhatja. A **Rendszergazda1** lehet az a felhasználó, aki jelenleg be van jelentkezve.

    | Name | Címtárszerepkör |
    | --- | --- |
    | **Rendszergazda1** | Globális rendszergazda<br/>-vagy-<br/>Felhasználói rendszergazda |
    | **Requestor1** | User |

1. Hozzon létre egy **marketing-erőforrások** nevű Azure ad biztonsági csoportot a **hozzárendelt** tagsági típussal.

    Ez a csoport lesz a jogosultságok kezelésének cél erőforrása. A csoportnak üresnek kell lennie a tagoktól a kezdéshez.

## <a name="step-2-create-an-access-package"></a>2. lépés: hozzáférési csomag létrehozása

A *hozzáférési csomag* olyan erőforrások kötege, amelyekhez egy csoportnak vagy projektnek szüksége van, és amelyekre szabályzat vonatkozik. A hozzáférési csomagok a *katalógusok* nevű tárolókban vannak meghatározva. Ebben a lépésben létrehoz egy **marketingkampány** -hozzáférési csomagot az **általános** katalógusban.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

![Hozzáférési csomag létrehozása](./media/entitlement-management-access-package-first/elm-access-package.png)

1. A Azure Portal a bal oldali navigációs sávon kattintson a **Azure Active Directory** elemre.

2. A bal oldali menüben kattintson az **identitások szabályozása** elemre.

3. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre.  Ha a **hozzáférés megtagadva** érték jelenik meg, győződjön meg arról, hogy a címtárban van egy prémium szintű Azure ad P2-licenc.

4. Kattintson az **új hozzáférési csomag** elemre.

    ![Jogosultságok kezelése a Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

5. Az **alapvető beállítások** lapon írja be a **marketing kampány** hozzáférési csomagjának és leírásának nevet a **kampány erőforrásaihoz**.

6. Hagyja meg a **katalógus** legördülő lista **általános** értékre állítását.

    ![Új hozzáférési csomag – alapismeretek lap](./media/entitlement-management-access-package-first/basics.png)

7. Kattintson a **tovább** gombra az **erőforrás-szerepkörök** lap megnyitásához.

    Ezen a lapon kiválaszthatja a hozzáférési csomagban szerepeltetni kívánt erőforrásokat és erőforrás-szerepkört.

8. Kattintson **a csoportok és** csoportok elemre.

9. A csoportok kiválasztása panelen keresse meg és válassza ki a korábban létrehozott **marketing-erőforrások** csoportot.

     Alapértelmezés szerint a csoportok az általános katalóguson belül jelennek meg. Ha kijelöl egy csoportot az általános katalóguson kívül, amelyet láthat, ha bejelöli az **összes** megjelenítése jelölőnégyzetet, a rendszer hozzáadja az általános katalógushoz.

    ![Képernyőkép, amely az "új hozzáférési csomag – erőforrás-szerepkörök" lapot és a "csoportok kiválasztása" ablakot jeleníti meg.](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

10. Kattintson a **kiválasztás** elemre a csoport listához való hozzáadásához.

11. A **szerepkör** legördülő listában válassza a **tag** elemet.

    ![Új hozzáférési csomag – erőforrás-szerepkörök lap](./media/entitlement-management-access-package-first/resource-roles.png)

    >[!IMPORTANT]
    >A hozzáférési csomaghoz hozzáadott szerepkör-hozzárendelhető csoportok a **szerepkörökhöz hozzárendelhető** altípussal lesznek jelezve. Az Azure AD-szerepkörökhöz hozzárendelhető csoportokkal kapcsolatos további részletekért tekintse meg a [szerepkörhöz hozzárendelhető csoport létrehozása](../roles/groups-create-eligible.md) a Azure Active Directoryban című témakört. Ne feledje, hogy ha egy szerepkör-hozzárendelésre jogosult csoport megtalálható egy hozzáférési csomag katalógusában, a jogosultságok kezelésében felügyeletre jogosult rendszergazda felhasználók, beleértve a globális rendszergazdákat, a felhasználói rendszergazdákat és a katalógus tulajdonosait, a katalógusban is vezérelhetik a hozzáférési csomagokat, így kiválaszthatják, hogy kik adhatók hozzá ezekhez a csoportokhoz. Ha nem jelenik meg a felvenni kívánt szerepkörrel rendelkező csoport, vagy ha nem tudja felvenni, győződjön meg arról, hogy rendelkezik a szükséges Azure AD-szerepkörrel és jogosultság-kezelési szerepkörrel a művelet végrehajtásához. Előfordulhat, hogy meg kell kérnie valakit, aki a szükséges szerepkörökkel hozzáadja az erőforrást a katalógushoz. További információ: a [szükséges szerepkörök az erőforrások katalógushoz való hozzáadásához](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

    >[!NOTE]
    > [Dinamikus csoportok](../enterprise-users/groups-create-rule.md) használatakor a tulajdonoson kívül más szerepkörök is nem érhetők el. Ez az elvárt működés.
    > ![A forgatókönyv áttekintése](./media/entitlement-management-access-package-first/dynamic-group-warning.png)
    


12. A **tovább** gombra kattintva nyissa meg a **kérelmek** lapot.

    Ezen a lapon létrehoz egy kérési házirendet. A *szabályzatok határozzák* meg a hozzáférési csomag elérésére vonatkozó szabályokat vagy guardrails. Olyan házirendet hoz létre, amely lehetővé teszi egy adott felhasználó számára az erőforrás-címtárban a hozzáférési csomag igénylését.

13. A **hozzáférést kérő felhasználók** területen kattintson a **címtárban lévő felhasználók** elemre, majd az **adott felhasználók és csoportok** elemre.

    ![Új hozzáférési csomag – kérelmek lap](./media/entitlement-management-access-package-first/requests.png)

14. Kattintson **a felhasználók és csoportok hozzáadása** lehetőségre.

15. A felhasználók és csoportok kiválasztása panelen válassza ki a korábban létrehozott **Requestor1** -felhasználót.

    ![Új hozzáférési csomag – kérelmek lap – felhasználók és csoportok kiválasztása](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

16. Kattintson a **Kiválasztás** elemre.

17. Görgessen le a **jóváhagyás** és a **kérelmek engedélyezése** szakaszban.

18. A **jóváhagyáshoz** a **nem** érték megadása szükséges.

19. A **kérelmek engedélyezéséhez** kattintson az **Igen** gombra, hogy a hozzáférési csomagot a létrehozásuk után azonnal meg lehessen kérni.

    ![Új hozzáférési csomag – kérelmek lap – jóváhagyás és a kérelmek engedélyezése](./media/entitlement-management-access-package-first/requests-approval-enable.png)

20. Az **életciklus** lap megnyitásához kattintson a **tovább** gombra.

21. A **lejárat** szakaszban adja meg a **hozzáférési csomag hozzárendeléseinek érvényességét** a **napok száma** szerint.

22. A **hozzárendelések** beállítása **30** nap után lejár.

    ![Új hozzáférési csomag – életciklus lap](./media/entitlement-management-access-package-first/lifecycle.png)

23. A **tovább** gombra kattintva nyissa meg a **felülvizsgálat + létrehozás** lapot.

    ![Új hozzáférési csomag – áttekintés + Létrehozás lap](./media/entitlement-management-access-package-first/review-create.png)

    Néhány pillanat múlva megjelenik egy értesítés arról, hogy a hozzáférési csomag létrehozása sikeres volt.

24. A marketing kampány hozzáférési csomagjának bal oldali menüjében kattintson az **Áttekintés** elemre.

25. Másolja a **saját hozzáférési portál hivatkozást**.

    Ezt a hivatkozást fogja használni a következő lépéshez.

    ![Hozzáférési csomag áttekintése – saját hozzáférési portál hivatkozása](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>3. lépés: hozzáférés kérése

Ebben a lépésben a lépéseket a **belső kérelmezőnek** kell elvégeznie, és hozzáférést kell kérnie a hozzáférési csomaghoz. A kérelmező a saját hozzáférési portál nevű hely használatával küldi el a kérelmeit. A saját hozzáférési portál lehetővé teszi, hogy a kérők beküldjék a hozzáférési csomagokat, és megtekintsék a hozzáférési csomagokat, amelyekre már hozzáférhetnek, és megtekinthetik a kérelmek előzményeit.

**Előfeltételként szükséges szerepkör:** Belső kérelmező

1. Jelentkezzen ki a Azure Portalból.

1. Egy új böngészőablakban navigáljon az előző lépésben másolt saját hozzáférési portál hivatkozásra.

1. Jelentkezzen be a saját hozzáférési portálra **Requestor1** néven.

    Ekkor meg kell jelennie a **marketing kampány** hozzáférési csomagjának.

1. Szükség esetén a **Leírás** oszlopban kattintson a nyílra a hozzáférési csomag részleteinek megtekintéséhez.

    ![Hozzáférési portál – hozzáférési csomagok](./media/entitlement-management-shared/my-access-access-packages.png)

1. Kattintson a pipa jelre a csomag kiválasztásához.

1. Kattintson a **hozzáférés kérése** elemre a hozzáférés kérése panel megnyitásához.

    ![Hozzáférési portál – hozzáférés kérése gomb](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. Az **üzleti indoklás** mezőbe írja be azt az indoklást, amelyet **az új marketingkampány során dolgozom**.

    ![Hozzáférési portál – hozzáférés kérése](./media/entitlement-management-shared/my-access-request-access.png)

1. Kattintson a **Submit (Küldés**) gombra.

1. A bal oldali menüben kattintson a **kérelmek előzményei** elemre, és ellenőrizze, hogy elküldte-e a kérést.

## <a name="step-4-validate-that-access-has-been-assigned"></a>4. lépés: annak ellenőrzése, hogy a hozzáférés hozzá van-e rendelve

Ebben a lépésben megerősíti, hogy a **belső kérelmező** hozzá lett rendelve a hozzáférési csomaghoz, és hogy most már a **marketing-erőforrások** csoport tagja.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. Jelentkezzen ki a saját hozzáférési portálról.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) **Rendszergazda1**.

1. Kattintson a **Azure Active Directory** , majd az **identitás-szabályozás** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre.

1. Keresse meg és kattintson a **marketing kampány** hozzáférési csomag elemre.

1. A bal oldali menüben kattintson a **kérelmek** elemre.

    Ekkor meg kell jelennie a Requestor1 és a kezdeti házirendnek, amelynek állapota **kézbesítés**.

1. A kérelem részleteinek megtekintéséhez kattintson a kérelemre.

    ![Hozzáférési csomag – kérelem részletei](./media/entitlement-management-access-package-first/request-details.png)

1. A bal oldali navigációs sávon kattintson a **Azure Active Directory** elemre.

1. Kattintson a **csoportok** elemre, és nyissa meg a **marketing-erőforrások** csoportot.

1. Kattintson a **tagok** elemre.

    Ekkor meg kell jelennie a tag **Requestor1** .

    ![Marketing-erőforrások tagjai](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>5. lépés: erőforrások törlése

Ebben a lépésben eltávolítja a végrehajtott módosításokat, és törli a **marketing kampány** hozzáférési csomagját.

**Előfeltételként szükséges szerepkör:**  Globális rendszergazda vagy felhasználói rendszergazda

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. Nyissa meg a **marketing kampány** hozzáférési csomagját.

1. Kattintson a **hozzárendelések** elemre.

1. A **Requestor1** kattintson a három pontra (**...**), majd a **hozzáférés eltávolítása** elemre. A megjelenő üzenetben kattintson az **Igen** gombra.

    Néhány pillanat elteltével az állapot a kézbesítéstől a Lejártig változik.

1. Kattintson az **erőforrás-szerepkörök** elemre.

1. **Marketing-erőforrások** esetében kattintson a három pontra (**...**), majd az **erőforrás-szerepkör eltávolítása** elemre. A megjelenő üzenetben kattintson az **Igen** gombra.

1. Nyissa meg a hozzáférési csomagok listáját.

1. **Marketingkampány** esetén kattintson a három pontra (**...**), majd a **Törlés** gombra. A megjelenő üzenetben kattintson az **Igen** gombra.

1. A Azure Active Directory törölje a létrehozott felhasználókat, például a **Requestor1** és a **Rendszergazda1**.

1. Törölje a **marketing-erőforrások** csoportot.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megismerheti a jogosultságok kezelésének gyakori forgatókönyveit.
> [!div class="nextstepaction"]
> [Gyakori forgatókönyvek](entitlement-management-scenarios.md)
