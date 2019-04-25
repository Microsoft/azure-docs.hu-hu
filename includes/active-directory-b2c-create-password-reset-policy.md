---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 78abb190dccd27c5bf70dfe12f978e1118601815
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456939"
---
Ahhoz, hogy a minden részletre kiterjedő jelszó kérését az alkalmazásban, használhat egy **új jelszó kérésére vonatkozó** felhasználói folyamatot. Vegye figyelembe, hogy a bérlői szintű jelszó-visszaállítási beállítás van megadva [Itt](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Ez a felhasználói folyamat írja le, hogy a felhasználók jelszó-visszaállítás és a jogkivonatokat, amelyeket az alkalmazás fogad a sikeres végrehajtása során.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Alatt **kezelés**válassza **felhasználói folyamatok** , és kattintson a +**új felhasználói folyamat**.

![Válassza ki az új felhasználói folyamat](./media/active-directory-b2c-create-password-reset-policy/add-b2c-new-user-flow.png)

Az a **ajánlott** lapon jelölje be **új jelszó kérésére vonatkozó**.

Adja meg a felhasználói folyamat **neve** , hogy az alkalmazás hivatkozni. Adja meg például a következőt: `SSPR`.

A **Identitásszolgáltatók**, ellenőrizze **jelszó visszaállítása e-mail-címmel**.

![Adjon meg nevet, és válassza ki jelszó visszaállítása e-mail-címmel lehetőséget Identitásszolgáltatóként](./media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

Alatt **alkalmazásjogcímek**, kattintson a **Továbbiak megjelenítése** , és válassza a jogcímek kívánt visszaküldött hitelesítő jogkivonatokban az alkalmazásnak a sikeres jelszókérési művelet után. Válassza például a **Felhasználó objektumazonosítója** lehetőséget.

Kattintson az **OK** gombra.

![Válasszon ki néhány alkalmazásjogcímet, majd kattintson az OK gombra.](./media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Kattintson a **létrehozás** hozzáadása a felhasználói folyamatot. A felhasználói folyamat állapottal **B2C_1_SSPR**. A **B2C_1_** előtagot a rendszer adja hozzá a névhez.

Kattintson a **felhasználói folyamat futtatása**. Ellenőrizze a táblázatban megadott beállításokat, majd kattintson a **felhasználói folyamat futtatása**.

![Válassza ki a felhasználói folyamat és futtatása](./media/active-directory-b2c-create-password-reset-policy/add-b2c-sspr-run-user-flow.png)

| Beállítás      | Value  |
| ------------ | ------ |
| **Alkalmazás** | Contoso B2C-alkalmazás |
| **Válasz URL-cím kiválasztása** | `https://localhost:44316/` |

Megnyílik egy böngészőablak, ahol ellenőrizheti, hogyan működik az alkalmazása jelszókérési felhasználói folyamata.

> [!NOTE]
> A szabályzat létrehozása és a frissítések érvénybe lépése akár egy percig is eltarthat.
>

