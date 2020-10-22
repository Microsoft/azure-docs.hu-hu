---
title: B2B külső együttműködési beállítások engedélyezése – Azure AD
description: Megtudhatja, hogyan engedélyezheti Active Directory B2B külső együttműködését, és hogyan kezelheti a vendég felhasználókat meghívó személyeket. A meghívók delegálásához használja a vendég meghívó szerepkört.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 09/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a983931bd372931eacff2f7b21f3358f536046
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92362926"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Külső B2B-együttműködés engedélyezése és a vendégmeghívási jogosultság kezelése

Ez a cikk bemutatja, hogyan engedélyezheti a Azure Active Directory (Azure AD) B2B-együttműködést, kijelölheti, hogy kik hívhatják meg a vendégeket, és meghatározhatja a vendég felhasználói számára az Azure AD-ban található engedélyeket. 

Alapértelmezés szerint a címtárban lévő összes felhasználó és vendég meghívhatja a vendégeket, még akkor is, ha nincsenek rendszergazdai szerepkörhöz rendelve. A külső együttműködési beállítások lehetővé teszik a vendég meghívások be-és kikapcsolását a szervezet különböző típusaihoz. Az egyes felhasználóknak küldött meghívókat olyan szerepkörök hozzárendelésével is delegálhatja, amelyek lehetővé teszik a vendégek meghívását.

Az Azure AD lehetővé teszi az Azure ad-címtárban lévő külső vendégek által megjelenített felhasználók korlátozását. Alapértelmezés szerint a vendég felhasználók korlátozott jogosultsági szintre vannak beállítva, amelyek letiltják a felhasználók, csoportok vagy más címtárbeli erőforrások számbavételét, de lehetővé teszi, hogy a nem rejtett csoportok tagságát lássák. Az új előzetes verzió lehetővé teszi, hogy a vendég hozzáférését még tovább korlátozza, hogy a vendégek csak a saját profiljuk adatait tudják megtekinteni. Részletekért lásd: a [vendég hozzáférési engedélyeinek korlátozása (előzetes verzió)](../enterprise-users/users-restrict-guest-permissions.md).

## <a name="configure-b2b-external-collaboration-settings"></a>B2B külső együttműködési beállítások konfigurálása

Az Azure AD B2B együttműködés révén a bérlői rendszergazdák a következő Meghívási szabályzatokat állíthatják be:

- Meghívók kikapcsolása
- Csak a vendég meghívó szerepkörben található rendszergazdák és felhasználók hívhatják meg
- Rendszergazdák, a vendég meghívó szerepkör, és a tagok meghívhatják
- Minden felhasználó, a vendégeket is meghívhatja

Alapértelmezés szerint az összes felhasználó, köztük a vendégek is meghívhatják a vendég felhasználókat.

### <a name="to-configure-external-collaboration-settings"></a>A külső együttműködési beállítások konfigurálása:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) bérlői rendszergazdaként.
2. Válassza a **Azure Active Directory**lehetőséget.
3. Külső **identitások**  >  **külső együttműködési beállítások**kiválasztása.

4. A **vendég felhasználói hozzáférési korlátozások (előzetes verzió)** területen válassza ki azt a hozzáférési szintet, amelyet a vendég felhasználóknak szeretne:

   - **A vendég felhasználók ugyanazokkal a hozzáféréssel rendelkeznek, mint a tagok (a legtöbb szolgáltató)**: Ez a beállítás lehetővé teszi, hogy a felhasználók ugyanazt az Azure ad-erőforrásokhoz és a címtáradatok eléréséhez férhessenek hozzá.

   - A **vendég felhasználók korlátozott hozzáféréssel rendelkeznek a címtárobjektumok tulajdonságaihoz és tagságához**: (alapértelmezés) Ez a beállítás letiltja a vendégeket bizonyos címtárbeli feladatokból, például a felhasználók, csoportok vagy más címtárbeli erőforrások enumerálásával. A vendégek megtekinthetik az összes nem rejtett csoport tagságát.

   - A **vendég felhasználói hozzáférés a saját címtár objektumainak tulajdonságaira és tagságára korlátozódik (a legszigorúbb)**: ezzel a beállítással a vendégek csak a saját profiljaikat érhetik el. A vendégek nem tekinthetik meg más felhasználók profilját, csoportját vagy csoporttagságok megjelenítését.
  
    ![Vendég felhasználói hozzáférés korlátozásának beállításai](./media/delegate-invitations/guest-user-access.png)

5. A **vendég Meghívási beállítások**területen válassza ki a megfelelő beállításokat:

   - **Meghívhatják a rendszergazdákat és felhasználókat a vendég meghívó szerepkörben**: Ha engedélyezni szeretné a rendszergazdák és a felhasználók számára a "vendég meghívót" szerepkört a vendégek meghívásához, állítsa ezt a házirendet **Igen**értékre.

   - A **tagok meghívhatják**: ha lehetővé szeretné tenni a címtár nem rendszergazdai tagjai számára a vendégek meghívását, állítsa **Igen**értékre a szabályzatot.

   - A **vendégek meghívhatják**: ha lehetővé szeretné tenni a vendégek számára más vendégek meghívását, állítsa **Igen**értékre a szabályzatot.

   - **E-mail One-Time PIN-kód engedélyezése a vendégek számára (előzetes verzió)**: az egyszeri PIN-kód szolgáltatással kapcsolatos további információkért tekintse meg az [e-mailek egyszeri jelszavas hitelesítése (előzetes verzió)](one-time-passcode.md)című témakört.

   - **Vendég önkiszolgáló regisztráció engedélyezése felhasználói folyamatokon keresztül (előzetes verzió)**: a beállítással kapcsolatos további információkért lásd: önkiszolgáló [bejelentkezési felhasználói folyamat hozzáadása egy alkalmazáshoz (előzetes verzió)](self-service-sign-up-user-flow.md).

   > [!NOTE]
   > Ha a **tagok meghívása** **nem** értékre van állítva, és **a vendég meghívó szerepkörben lévő felhasználók meghívhatják** az **Igen**értéket, akkor a vendég **meghívó** szerepkörben lévő felhasználók továbbra is meghívhatják a vendégeket.

    ![Vendég Meghívási beállításai](./media/delegate-invitations/guest-invite-settings.png)

6. Az **együttműködési korlátozások**területen válassza ki, hogy engedélyezi vagy letiltja a meghívókat a megadott tartományokhoz. További információ: a [vállalatközi felhasználók meghívásának engedélyezése vagy letiltása adott szervezetekben](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>A vendég meghívó szerepkör társítása egy felhasználóhoz

A vendég meghívó szerepkörrel egyéni felhasználók is meghívhatják a vendégeket, anélkül, hogy globális rendszergazdai vagy egyéb rendszergazdai szerepkört rendeljenek hozzájuk. Rendelje hozzá a vendég meghívó szerepkört az egyéni felhasználókhoz. Ezután győződjön meg arról, hogy **a rendszergazdák és a felhasználók megadhatják a vendég meghívó szerepkört** **Igen**értékre.

Az alábbi példa bemutatja, hogyan adhat hozzá felhasználót a vendég meghívó szerepkörhöz a PowerShell használatával:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az Azure AD B2B együttműködésről:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B Collaboration Guest Users hozzáadása meghívás nélkül](add-user-without-invite.md)
- [B2B csoportmunka-felhasználó hozzáadása egy szerepkörhöz](add-guest-to-role.md)

