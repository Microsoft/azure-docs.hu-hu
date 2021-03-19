---
title: VÁLLALATKÖZI együttműködéssel rendelkező felhasználók hozzáadása a Azure Portal-Azure AD-ben
description: Azt mutatja, hogy a rendszergazda hogyan adhat vendég felhasználókat a címtárhoz Azure Active Directory (Azure AD) B2B-együttműködés használatával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2566b427777aec273df17863f06040de20649d97
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581847"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Azure Active Directory B2B Collaboration-felhasználók hozzáadása a Azure Portal

A korlátozott rendszergazdai címtárbeli szerepkörökhöz hozzárendelt felhasználók a Azure Portal használatával meghívhatják a B2B együttműködési felhasználókat. Meghívhatja a vendég felhasználókat a címtárba, egy csoportba vagy egy alkalmazásba. Miután meghívott egy felhasználót ezen módszerek bármelyikén keresztül, a meghívott felhasználó fiókja hozzá lesz adva a Azure Active Directoryhoz (Azure AD) a *vendég* felhasználói típusával. Ezután a vendég felhasználónak be kell váltania a meghívót az erőforrásokhoz való hozzáférésre. Egy felhasználó meghívása nem jár le.

Miután hozzáadta a vendég felhasználót a címtárhoz, elküldheti a vendég felhasználó közvetlen hivatkozását egy megosztott alkalmazásra, vagy a vendég felhasználó a meghívót tartalmazó e-mailben a beváltási URL-címre is kattinthat. További információ a beváltási folyamatról: [vállalatközi együttműködés meghívásának beváltása](redemption-experience.md).

> [!IMPORTANT]
> A szervezete adatvédelmi nyilatkozatának URL-címének hozzáadásához kövesse a következő témakörben ismertetett lépéseket [: útmutató: a szervezet adatvédelmi adatainak hozzáadása a Azure Active Directory](../fundamentals/active-directory-properties-area.md) . A meghívó első beváltási folyamatának részeként a meghívott felhasználónak el kell fogadnia az adatvédelmi feltételeit a folytatáshoz. 

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy a szervezet külső együttműködési beállításai úgy vannak konfigurálva, hogy Ön is meghívja vendégeit. Alapértelmezés szerint minden felhasználó és rendszergazda meghívhatja a vendégeket. Előfordulhat azonban, hogy a szervezet külső együttműködési házirendjei úgy vannak konfigurálva, hogy megakadályozzák bizonyos típusú felhasználók vagy rendszergazdák számára a vendégek meghívását. Ha szeretné megtudni, hogyan tekintheti meg és állíthatja be ezeket a szabályzatokat, tekintse meg a [B2B külső együttműködés engedélyezése és a felhasználók meghívása](delegate-invitations.md)című témakört.

## <a name="add-guest-users-to-the-directory"></a>Vendég felhasználók hozzáadása a címtárhoz

Ha VÁLLALATKÖZI együttműködési felhasználókat szeretne hozzáadni a címtárhoz, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) olyan felhasználóként, aki korlátozott rendszergazdai címtárbeli szerepkörrel vagy a vendég meghívó szerepkörrel van társítva.
2. Keresse meg és válassza ki a **Azure Active Directory** bármely oldalon.
3. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
4. Válassza az **Új vendégfelhasználót**.

   ![Azt mutatja, hogy az új vendég felhasználó hol van a felhasználói felületen](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
5. Az **új felhasználó** lapon válassza a **felhasználó meghívása** lehetőséget, majd adja hozzá a vendég felhasználó adatait. 

    > [!NOTE]
    > A csoportos e-mail-címek nem támogatottak; adja meg az egyéni e-mail-címet. Emellett egyes e-mail-szolgáltatók lehetővé teszik a felhasználók számára egy pluszjel (+) és egy további szöveg hozzáadását az e-mail-címükhez, így például a beérkezett fájlok szűréséhez. Az Azure AD azonban jelenleg nem támogatja az e-mail-címeken a plusz szimbólumokat. A kézbesítési problémák elkerüléséhez hagyja ki a pluszjelet és az azt követő karaktereket a @ szimbólumra.

   - **név.** A vendég felhasználó vezetékneve és vezetékneve.
   - **E-mail-cím (kötelező)**. A vendég felhasználó e-mail-címe.
   - **Személyes üzenet (nem kötelező)** Személyes üdvözlő üzenet felvétele a vendég felhasználóra.
   - **Csoportok**: felveheti a vendég felhasználót egy vagy több meglévő csoportba, vagy később is megteheti.
   - **Címtárbeli szerepkör**: Ha az Azure ad rendszergazdai engedélyekre van szüksége a felhasználó számára, hozzáadhat egy Azure ad-szerepkörhöz. 

7. Válassza ki **Meghívás** elemet az meghívó automatikus elküldéséhez a vendégfelhasználó számára. 
 
Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.


![A vendég felhasználói típussal rendelkező B2B-felhasználó megjelenítése](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Vendégfelhasználók hozzáadása egy csoporthoz
Ha a VÁLLALATKÖZI együttműködési felhasználókat manuálisan kell hozzáadnia egy csoporthoz, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként.
2. Keresse meg és válassza ki a **Azure Active Directory** bármely oldalon.
3. A **kezelés** területen válassza a **csoportok** lehetőséget.
4. Válasszon ki egy csoportot (vagy kattintson az **új csoport** elemre egy új létrehozásához). Érdemes belefoglalni a csoport leírásában, hogy a csoport B2B vendég felhasználókat tartalmaz.
5. Válassza ki a **Members** (Tagok) elemet. 
6. Tegye a következők egyikét:
   - Ha a vendég felhasználó már létezik a címtárban, keresse meg a B2B felhasználót. Válassza ki a felhasználót, majd kattintson a **kiválasztás** elemre a felhasználó csoportba való felvételéhez.
   - Ha a vendég felhasználó még nem létezik a címtárban, hívja meg őket a csoportba úgy, hogy beírja az e-mail-címét a keresőmezőbe, írja be az opcionális személyes üzenetet, majd kattintson a **kiválasztás** gombra. A meghívás automatikusan kikerül a meghívott felhasználó felé.
     
     ![Meghívás hozzáadása gomb a vendég tagok hozzáadásához](./media/add-users-administrator/GroupInvite.png)
   
Dinamikus csoportokat is használhat az Azure AD B2B együttműködés segítségével. További információ: [dinamikus csoportok és Azure Active Directory B2B együttműködés](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Vendégfelhasználók hozzáadása egy alkalmazáshoz

Ha VÁLLALATKÖZI együttműködéssel rendelkező felhasználókat szeretne felvenni egy alkalmazásba, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként.
2. Keresse meg és válassza ki a **Azure Active Directory** bármely oldalon.
3. A **kezelés** területen válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
4. Válassza ki azt az alkalmazást, amelyhez vendég felhasználókat szeretne felvenni.
5. Az alkalmazás irányítópultján válassza az **összes felhasználó** lehetőséget a **felhasználók és csoportok** ablaktábla megnyitásához.

    ![Összes felhasználó gomb a nyitott felhasználók és csoportok hozzáadásához](./media/add-users-administrator/AppUsersAndGroups.png)

6. Válassza a **Felhasználó hozzáadása** elemet.
7. A **hozzárendelés hozzáadása** területen válassza a **felhasználók és csoportok** lehetőséget.
8. Tegye a következők egyikét:
   - Ha a vendég felhasználó már létezik a címtárban, keresse meg a B2B felhasználót. Válassza ki a felhasználót, kattintson a **kiválasztás** elemre, majd kattintson a **hozzárendelés** gombra a felhasználó az alkalmazáshoz való hozzáadásához.
   - Ha a vendég felhasználó még nem létezik a címtárban, a **tag kiválasztása vagy a külső felhasználó meghívása** területen írja be a felhasználó e-mail-címét. Az üzenet mezőbe írjon be egy opcionális személyes üzenetet. Az üzenet mezőben kattintson a **meghívás** elemre.
           
       ![Képernyőfelvétel: a felhasználó e-mail-címének hozzáadása, a személyre szabott üzenet és a Meghívási gomb kiemelése.](./media/add-users-administrator/AppInviteUsers.png)
   
      Kattintson a **kiválasztás** elemre, majd kattintson a **hozzárendelés** gombra a felhasználó az alkalmazáshoz való hozzáadásához. Egy meghívás automatikusan kikerül a meghívott felhasználó felé.

9. A vendég felhasználó megjelenik az alkalmazás **felhasználók és csoportok** listájában az **alapértelmezett hozzáférés** hozzárendelt szerepkörével. Ha módosítani szeretné a szerepkört, tegye a következőket:
   - Válassza ki a vendég felhasználót, majd válassza a **Szerkesztés** lehetőséget. 
   - A **hozzárendelés szerkesztése** alatt kattintson a **szerepkör kiválasztása** elemre, és válassza ki a kiválasztott felhasználóhoz hozzárendelni kívánt szerepkört.
   - Kattintson a **Kiválasztás** elemre.
   - Kattintson a **Hozzárendelés** gombra.
 
## <a name="resend-invitations-to-guest-users"></a>Meghívók újraküldése a vendégfelhasználóknak

Ha a vendég felhasználó még nem váltotta be a meghívót, akkor újra elküldheti a meghívót tartalmazó e-mailt.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként.
2. Keresse meg és válassza ki a **Azure Active Directory** bármely oldalon.
3. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
5. Válassza ki a felhasználói fiókot.
6. A **kezelés** területen válassza a **profil** lehetőséget.
7. Ha a felhasználó még nem fogadta el a meghívót, az **identitás** szakaszban a **meghívást** a **nem** értékre állítja a rendszer. A meghívó újraküldéséhez válassza a **(kezelés)** lehetőséget. Ezután a **meghívások kezelése** lapon a * * Újraküldés újraküldése lehetőség mellett Válassza az **Igen**, majd a **kész** lehetőséget.

> [!NOTE]
> Ha olyan meghívót küld újra, amely eredetileg a felhasználót egy adott alkalmazáshoz irányította, akkor az új meghívóban található hivatkozás helyett a legfelső szintű hozzáférési panelre kerül.
> Emellett csak a meghívó engedéllyel rendelkező felhasználók küldhetik újra a meghívókat.

## <a name="next-steps"></a>Következő lépések

- Annak megismeréséhez, hogy a nem Azure AD-rendszergazdák hogyan vehetik fel a B2B vendég felhasználókat, tekintse meg az [információkkal dolgozó szakemberek a B2B Collaboration-felhasználókat?](add-users-information-worker.md)
- A meghívó e-mail-címével kapcsolatos információkért tekintse meg a [B2B együttműködés meghívójának e-mail-címét](invitation-email-elements.md).
