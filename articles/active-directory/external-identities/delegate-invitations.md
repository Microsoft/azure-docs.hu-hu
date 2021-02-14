---
title: B2B külső együttműködési beállítások engedélyezése – Azure AD
description: Megtudhatja, hogyan engedélyezheti Active Directory B2B külső együttműködését, és hogyan kezelheti a vendég felhasználókat meghívó személyeket. A meghívók delegálásához használja a vendég meghívó szerepkört.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68ed67afc949b3a67936f20a098bca8b6906a598
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365548"
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
2. Válassza a **Azure Active Directory** lehetőséget.
3. Külső **identitások**  >  **külső együttműködési beállítások** kiválasztása.

4. A **vendég felhasználói hozzáférési korlátozások (előzetes verzió)** területen válassza ki azt a hozzáférési szintet, amelyet a vendég felhasználóknak szeretne:
  
   - **A vendég felhasználók ugyanazokkal a hozzáféréssel rendelkeznek, mint a tagok (a legtöbb szolgáltató)**: Ez a beállítás lehetővé teszi, hogy a felhasználók ugyanazt az Azure ad-erőforrásokhoz és a címtáradatok eléréséhez férhessenek hozzá.

   - A **vendég felhasználók korlátozott hozzáféréssel rendelkeznek a címtárobjektumok tulajdonságaihoz és tagságához**: (alapértelmezés) Ez a beállítás letiltja a vendégeket bizonyos címtárbeli feladatokból, például a felhasználók, csoportok vagy más címtárbeli erőforrások enumerálásával. A vendégek megtekinthetik az összes nem rejtett csoport tagságát.

   - A **vendég felhasználói hozzáférés a saját címtár objektumainak tulajdonságaira és tagságára korlátozódik (a legszigorúbb)**: ezzel a beállítással a vendégek csak a saját profiljaikat érhetik el. A vendégek nem tekinthetik meg más felhasználók profilját, csoportját vagy csoporttagságok megjelenítését.


5. A **vendég Meghívási beállítások** területen válassza ki a megfelelő beállításokat:

    ![Vendég Meghívási beállításai](./media/delegate-invitations/guest-invite-settings.png)

   - **Meghívhatják a rendszergazdákat és felhasználókat a vendég meghívó szerepkörben**: Ha engedélyezni szeretné a rendszergazdák és a felhasználók számára a "vendég meghívót" szerepkört a vendégek meghívásához, állítsa ezt a házirendet **Igen** értékre.

   - A **tagok meghívhatják**: ha lehetővé szeretné tenni a címtár nem rendszergazdai tagjai számára a vendégek meghívását, állítsa **Igen** értékre a szabályzatot.

   - A **vendégek meghívhatják**: ha lehetővé szeretné tenni a vendégek számára más vendégek meghívását, állítsa **Igen** értékre a szabályzatot.

   > [!NOTE]
   > Ha a **tagok meghívása** **nem** értékre van állítva, és **a vendég meghívó szerepkörben lévő felhasználók meghívhatják** az **Igen** értéket, akkor a vendég **meghívó** szerepkörben lévő felhasználók továbbra is meghívhatják a vendégeket.

6. Válassza ki a megfelelő beállításokat a **levelezés egyszeri jelszava a vendégek** számára területen. További információért lásd az [e-mail egyszeri jelszavas hitelesítését](one-time-passcode.md)ismertető témakört:

   - **E-mail egyszeri PIN-kód automatikus engedélyezése a vendégek számára a 2021 októberi időszakban**. Alapértelmezett Ha az egyszer használatos e-mail-jelszó funkció még nincs engedélyezve a bérlőnél, a rendszer automatikusan bekapcsolja a 2021-es időpontban. Ha azt szeretné, hogy a szolgáltatás egyszerre legyen engedélyezve, nincs szükség további műveletekre. Ha már engedélyezte vagy letiltotta a szolgáltatást, ez a beállítás nem lesz elérhető.

   - **E-mailes egyszeri jelszó engedélyezése a vendégek számára most**. Bekapcsolja a bérlői e-mailek egyszeri jelszavas szolgáltatását.

   - **Tiltsa le az e-mailek egyszeri PIN-kódját a vendégek** számára. Kikapcsolja az e-mailek egyszeri jelszavas funkcióját a bérlő számára, és megakadályozza, hogy a funkció bekapcsoljon a 2021. októberi időszakra.

   > [!NOTE]
   > A fenti lehetőségek helyett a következő váltógomb jelenik meg, ha engedélyezte vagy letiltotta ezt a funkciót, vagy ha korábban már beállította az előzetes verziót:
   >
   >![E-mailek egyszeri jelszavas beállításának engedélyezése](media/delegate-invitations/enable-email-otp-opted-in.png)

7. A **vendég önkiszolgáló regisztráció engedélyezése felhasználói folyamatokon keresztül (előzetes verzió)** területen válassza az **Igen** lehetőséget, ha olyan felhasználói folyamatokat szeretne létrehozni, amelyek lehetővé teszik a felhasználók számára az alkalmazások regisztrálását. További információ erről a beállításról: önkiszolgáló [bejelentkezési felhasználói folyamat hozzáadása egy alkalmazáshoz (előzetes verzió)](self-service-sign-up-user-flow.md).

    ![Önkiszolgáló regisztráció felhasználói folyamatok beállításán keresztül](./media/delegate-invitations/self-service-sign-up-setting.png)

7. Az **együttműködési korlátozások** területen válassza ki, hogy engedélyezi vagy letiltja a meghívókat a megadott tartományokhoz. További információ: a [vállalatközi felhasználók meghívásának engedélyezése vagy letiltása adott szervezetekben](allow-deny-list.md).

    ![Együttműködési korlátozások beállításai](./media/delegate-invitations/collaboration-restrictions.png)
## <a name="assign-the-guest-inviter-role-to-a-user"></a>A vendég meghívó szerepkör társítása egy felhasználóhoz

A vendég meghívó szerepkörrel egyéni felhasználók is meghívhatják a vendégeket, anélkül, hogy globális rendszergazdai vagy egyéb rendszergazdai szerepkört rendeljenek hozzájuk. Rendelje hozzá a vendég meghívó szerepkört az egyéni felhasználókhoz. Ezután győződjön meg arról, hogy **a rendszergazdák és a felhasználók megadhatják a vendég meghívó szerepkört** **Igen** értékre.

Az alábbi példa bemutatja, hogyan adhat hozzá felhasználót a vendég meghívó szerepkörhöz a PowerShell használatával:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az Azure AD B2B együttműködésről:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B Collaboration Guest Users hozzáadása meghívás nélkül](add-user-without-invite.md)
- [B2B csoportmunka-felhasználó hozzáadása egy szerepkörhöz](add-guest-to-role.md)

