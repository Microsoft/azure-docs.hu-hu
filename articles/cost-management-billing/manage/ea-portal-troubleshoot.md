---
title: A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása
description: Ez a cikk bemutat néhány gyakori problémát, amelyek az Azure Nagyvállalati Szerződéssel kapcsolatban léphetnek fel az Azure EA Portalon.
author: bandersmsft
ms.author: banders
ms.date: 03/26/2021
ms.topic: troubleshooting
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 852fb85607318772870a8b6826c934997b84ff6d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726465"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása

Ez a cikk bemutat néhány gyakori problémát, amelyek az Azure Nagyvállalati Szerződéssel kapcsolatban léphetnek fel. Az Azure EA Portal a nagyvállalati szerződéses felhasználók és költségek kezelésére szolgál. Ezekkel a hibákkal az Azure EA Portalhoz való hozzáférés konfigurálása vagy frissítése során találkozhat.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Hibák egy rendszergazda regisztrációhoz való hozzáadásakor

Különböző hitelesítési szintek vannak a vállalati regisztrációkra vonatkozóan. Ha a hitelesítési szintek nem megfelelően vannak alkalmazva, hibák léphetnek fel az Azure EA Portalba való bejelentkezéskor.

Az Azure EA Portalon hozzáférést biztosíthat a különböző hitelesítési szintekkel rendelkező felhasználóknak. A vállalati rendszergazdák frissíthetik a hitelesítési szinteket, hogy megfeleljenek a szervezetük biztonsági követelményeinek.

### <a name="authentication-level-types"></a>Hitelesítési szintek típusai

- Csak Microsoft-fiók – azoknak a szervezeteknek, akik Microsoft-fiókokon keresztül szeretnék használni, létrehozni és felügyelni a felhasználókat.
- Munkahelyi vagy iskolai fiók – olyan szervezetek számára, amelyek felhőbeli összevonással telepítették az Active Directoryt, és az összes fiók egyetlen bérlőn található.
- Munkahelyi vagy iskolai fiókok több bérlőn – olyan szervezetek számára, amelyek felhőbeli összevonással telepítették az Active Directoryt, és a fiókjaik több bérlőn találhatók.
- Vegyes fiók – lehetővé teszi, hogy hozzáadjon Microsoft-fiókkal és/vagy munkahelyi vagy iskolai fiókkal rendelkező felhasználókat.

A regisztrációhoz elsőként hozzáadott munkahelyi vagy iskolai fiók határozza meg az _alapértelmezett_ tartományt. Amennyiben egy másik bérlővel rendelkező munkahelyi vagy iskolai fiókot kíván hozzáadni, több-bérlős hitelesítésre kell módosítania a hitelesítési szintet a regisztrációban.

A hitelesítési szint frissítése:

1. Jelentkezzen be az Azure Portalra vállalati rendszergazdaként.
2. Kattintson a **Kezelés** elemre a bal oldali navigációs panelen.
3. Kattintson a **Regisztráció** fülre.
4. A **Regisztráció részletei** alatt válassza a **Hitelesítési szint** elemet.
5. Kattintson a ceruza szimbólumra.
6. Kattintson a **Save** (Mentés) gombra.

![Hitelesítési szinteket bemutató példa ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Minden Microsoft-fióknak rendelkeznie kell egy, a [https://signup.live.com](https://signup.live.com/) címen létrehozott hozzárendelt azonosítóval.

A munkahelyi vagy iskolai fiókok olyan szervezetek számára érhetők el, amelyek felhőbeli összevonással telepítették az Active Directoryt, és amelyek esetében az összes fiók egy bérlőn található. A felhasználók munkahelyi vagy iskolai összevont felhasználói hitelesítéssel adhatók hozzá, ha a szervezet belső Active Directoryja összevont.

Amennyiben a szervezet nem használja az Active Directory összevonási szolgáltatásokat, nem használhatja a munkahelyi vagy iskolai e-mail-címét. Ehelyett regisztráljon vagy hozzon létre egy új e-mail-címet, és regisztrálja Microsoft-fiókként.

## <a name="unable-to-access-the-azure-ea-portal"></a>Nem lehet elérni az Azure EA Portalt

Ha hibaüzenetet kap az Azure EA Portalba történő bejelentkezéskor, végezze el az alábbi hibaelhárítási lépéseket:

- Győződjön meg arról, hogy a helyes Azure EA Portal URL-címet használja, vagyis: https://ea.azure.com.
- Állapítsa meg, hogy az Azure EA Portalhoz munkahelyi, iskolai vagy Microsoft-fiókkal regisztrált.
  - Ha a munkahelyi fiókját használja, adja meg munkahelyi e-mail-címét és jelszavát. A munkahelyi jelszavát a vállalata határozza meg. Felkeresheti a szervezet informatikai részlegét is, hogy megtudja, hogyan állíthatja vissza jelszavát, ha problémái vannak vele.
  - Ha Microsoft-fiókot használ, adja meg a Microsoft-fiókjához tartozó e-mail-címet és jelszót. Ha elfelejtette a Microsoft-fiókja jelszavát, itt állíthatja vissza: [https://account.live.com/password/reset](https://account.live.com/password/reset).
- A bejelentkezéshez privát vagy inkognitó módú böngésző-munkamenetet használjon, hogy megakadályozza a korábbi vagy a jelenlegi munkamenetek cookie-jainak és gyorsítótárazott adatainak mentését. Törölje a böngésző gyorsítótárát, és használjon privát vagy inkognitó módú ablakot a https://ea.azure.com webhely megnyitásához.
- A Microsoft-fiókja használatakor megjelenő _Érvénytelen felhasználó_ hiba azért jelenhet meg, mert több Microsoft-fiókkal rendelkezik. A bejelentkezési kísérletnél megadott e-mail-cím nem az elsődleges e-mail-cím.
Vagy azért is kaphat _Érvénytelen felhasználó_ hibaüzenetet, mert helytelen fióktípust használtak a felhasználónak a regisztrációhoz való hozzáadásakor. Elképzelhető például, hogy munkahelyi vagy iskolai fiókot használtak a Microsoft-fiók helyett. Ez esetben egy vállalati rendszergazda hozzáadhatja a megfelelő fiókot, vagy Ön kapcsolatba léphet a [támogatási szolgálattal](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).
  - Ha ellenőrizni szeretné az elsődleges aliast, látogasson el a [https://account.live.com](https://account.live.com) címre. Ezután kattintson a **Saját adatok**, majd a **Microsoft-fiókba való bejelentkezés módja** elemre. Az útmutatást követve ellenőrizze a másodlagos e-mail-címet, és igényeljen egy kódot a bizalmas információk eléréséhez. Adja meg a biztonsági kódot. Válassza a **Beállítás később** lehetőséget, ha nem szeretné beállítani a kétfaktoros hitelesítést.
  - Ezután megjelenik a **Microsoft-fiókba való bejelentkezés módja** oldal, ahol megtekintheti a fiókhoz tartozó meglévő aliasokat. Ellenőrizze, hogy az elsődleges aliast használja-e az Azure EA Portalra történő bejelentkezéshez. Ha nem, beállíthatja az elsődleges aliasaként, vagy használhatja az elsődleges aliasát az Azure EA Portalra való bejelentkezéshez.

## <a name="next-steps"></a>További lépések

- Az Azure EA Portal rendszergazdáinak érdemes elolvasniuk [a nagyvállalati szerződéses Azure Portal adminisztrációjáról](ea-portal-administration.md) szóló cikket a gyakori adminisztratív feladatok megismeréséhez.
- Az Azure [EA Cost Management + Billing aktiválás](../cost-management-billing-faq.yml) gyakori problémáival kapcsolatos kérdésekért és válaszokért olvassa el a gyakori kérdéseket és válaszokat.
