---
title: Ideiglenes hozzáférési pass beállítása az Azure AD-ben a jelszóval nem rendelkező hitelesítési módszerek regisztrálásához
description: Megtudhatja, hogyan konfigurálhatja és engedélyezheti a felhasználók számára a jelszó-alapú hitelesítési módszerek regisztrálását ideiglenes hozzáférési pass (KOPPINTÁS) használatával.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: justinha
author: inbarckms
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d45119fa86ab47e6a625c628d8cb9763db83bd
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520994"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Ideiglenes hozzáférési pass beállítása az Azure AD-ben a jelszóval nem rendelkező hitelesítési módszerek regisztrálásához (előzetes verzió)

A jelszó nélküli hitelesítési módszerek, például a FIDO2 és a jelszó nélküli telefonos bejelentkezés a Microsoft Authenticator alkalmazáson keresztül, lehetővé teszi a felhasználók számára, hogy jelszó nélkül biztonságosan jelentkezzenek be. A felhasználók a jelszóval nem rendelkező metódusok kétféleképpen jelentkezhetnek be:

- Meglévő Azure AD multi-Factor Authentication módszerek használata 
- Ideiglenes hozzáférési pass használata (KOPPINTÁS) 

A TAP egy olyan időkorlátos PIN-kód, amely megfelel az erős hitelesítési követelményeknek, és felhasználható más hitelesítési módszerek bevezetésére, beleértve a jelszó nélkülieket is. Ha a felhasználó elveszítette vagy elfelejtette az erős hitelesítési tényezőt, például egy FIDO2 biztonsági kulcsot vagy Microsoft Authenticator alkalmazást, KOPPINTson a helyreállítás megkönnyítése lehetőségre, de az új erős hitelesítési módszerek regisztrálásához be kell jelentkeznie.


Ebből a cikkből megtudhatja, hogyan engedélyezheti és használhatja az Azure AD-t a Azure Portal használatával. Ezeket a műveleteket a REST API-k használatával is végrehajthatja. 

>[!NOTE]
>Az ideiglenes hozzáférési pass jelenleg nyilvános előzetes verzióban érhető el. Előfordulhat, hogy egyes funkciók nem támogatottak vagy korlátozott képességekkel rendelkeznek. 

## <a name="enable-the-tap-policy"></a>A KOPPINTó házirend engedélyezése

A TAP-szabályzat meghatározza a beállításokat, például a bérlőben létrehozott pass-ket, illetve azokat a felhasználókat és csoportokat, akik használhatják a KOPPINTÁSt a bejelentkezéshez. Mielőtt bárki be tud jelentkezni egy KOPPINTÁSsal, engedélyeznie kell a hitelesítési módszer házirendjét, és ki kell választania, hogy mely felhasználók és csoportok jelentkezhetnek be egy KOPPINTÁSsal.
Bár bármelyik felhasználóhoz létrehozhat egy KOPPINTÁSt, csak a szabályzatban szereplők jelentkezhetnek be.

A globális rendszergazda és a hitelesítési módszer házirend-rendszergazdai szerepkör birtokosai frissíthetik az KOPPINTó hitelesítési módszer házirendjét.
A következő módszer megadásával konfigurálhatja a hitelesítési módszer házirendjét:

1. Jelentkezzen be a Azure Portal globális rendszergazdaként, és kattintson a **Azure Active Directory**  >  **biztonsági**  >  **hitelesítési módszerek**  >  **ideiglenes hozzáférési pass** elemre.
1. Az **Igen** gombra kattintva engedélyezheti a szabályzatot, kiválaszthatja, hogy mely felhasználók rendelkezzenek az alkalmazott szabályzattal, valamint az **általános** beállításokkal.

   ![A TAP hitelesítési módszer házirendjének engedélyezését bemutató képernyőkép](./media/how-to-authentication-temporary-access-pass/policy.png)

   Az alapértelmezett értéket és az engedélyezett értékek tartományát az alábbi táblázat ismerteti.


   | Beállítás          | Alapértelmezett értékek | Megengedett értékek               | Megjegyzések                                                                                                                                                                                                                                                                 |   |
   |------------------|----------------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    Minimális élettartam | 1 óra         | 10 – 43200 perc (30 nap) | A KOPPINTÁS érvényességének minimális száma percben.                                                                                                                                                                                                                         |   |
   | Maximális élettartam | 24 óra       | 10 – 43200 perc (30 nap) | A KOPPINTÁS érvényességi idejének maximális száma percben.                                                                                                                                                                                                                         |   |
   | Alapértelmezett élettartam | 1 óra         | 10 – 43200 perc (30 nap) | Az alapértelmezett értékeket felülbírálhatja az egyes fázisok, a házirend által beállított minimális és maximális élettartamon belül.                                                                                                                                                |   |
   | Egyszeri használat     | Hamis          | Igaz/hamis                 | Ha a házirend hamis értékre van állítva, akkor a bérlőben lévő átadások az érvényessége során egyszer vagy többször is használhatók (maximális élettartam). Ha a TAP-szabályzatban egyszer használatba veszi a használati időt, a bérlőben létrehozott összes hágó egyszeri használatra lesz létrehozva. |   |
   | Hossz           | 8              | 8-48 karakter              | Meghatározza a PIN-kód hosszát.                                                                                                                                                                                                                                      |   |

## <a name="create-a-tap-in-the-azure-ad-portal"></a>KOPPINTÁS létrehozása az Azure AD-portálon

Miután engedélyezte a TAP-szabályzatot, létrehozhat egy KOPPINTó felhasználót az Azure AD-ben. Ezek a szerepkörök a következő műveletekkel kapcsolatos műveleteket hajtják végre.

- A globális rendszergazda létrehozhat, törölhet, megtekintheti bármelyik felhasználót (kivéve magukat)
- Az emelt szintű hitelesítő rendszergazdák létrehozhatnak, törölhetnek, megtekinthetnek egy KOPPINTÁSt a rendszergazdák és a tagok számára (kivéve magukat)
- A hitelesítési rendszergazdák létrehozhatják, törölhetik, megtekinthetik a tagokat (kivéve magukat)
- A globális rendszergazda megtekintheti a felhasználó KOPPINTÁSának részleteit (a kód beolvasása nélkül).

KOPPINTÁS létrehozásához:

1. Jelentkezzen be a portálra globális rendszergazdaként, privilegizált hitelesítési rendszergazdaként vagy hitelesítési rendszergazdaként. 
1. Kattintson a **Azure Active Directory** gombra, tallózással keresse meg a felhasználók elemet, válasszon ki egy felhasználót, például: *Chris Green*, majd válassza a **hitelesítési módszerek** lehetőséget.
1. Ha szükséges, válassza az **új felhasználói hitelesítési módszerek kipróbálása** lehetőséget.
1. Válassza a **hitelesítési módszerek hozzáadásának** lehetőségét.
1. Válassza az alábbi **módszer lehetőséget**, majd kattintson az **ideiglenes hozzáférési pass (előzetes verzió)** lehetőségre.
1. Definiáljon egyéni aktiválási időt vagy időtartamot, majd kattintson a **Hozzáadás** gombra.

   ![A TAP létrehozásának képernyőképe](./media/how-to-authentication-temporary-access-pass/create.png)

1. A Hozzáadás után megjelennek a KOPPINTÁS részletei. Jegyezze fel a tényleges KOPPINTÁS értékét. Ezt az értéket a felhasználónak kell megadnia. Ezt az értéket nem tekintheti meg, miután rákattintott **az OK** gombra.
   
   ![A TAP-részletek képernyőképe](./media/how-to-authentication-temporary-access-pass/details.png)

## <a name="use-a-tap"></a>KOPPINTÁS használata

A TAP leggyakoribb felhasználási célja, hogy a felhasználók az első bejelentkezéskor regisztrálják a hitelesítési adatokat, anélkül, hogy további biztonsági kérdéseket kellene elvégezniük. A hitelesítési módszerek regisztrálva vannak a következő helyen: [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . A felhasználók itt is frissíthetik a meglévő hitelesítési módszereket.

1. Nyisson meg egy webböngészőt a következőhöz: [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Adja meg annak a fióknak az egyszerű felhasználónevét, amelyhez a KOPPINTott, például: *tapuser@contoso.com* .
1. Ha a felhasználó szerepel a TAP-szabályzatban, megjelenik egy képernyő, ahol megadhatja a KOPPINTÁSt.
1. Adja meg a Azure Portalban megjelenített KOPPINTÁSt.

   ![A KOPPINTÁS beírásának képernyőképe](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>Összevont tartományok esetén a rendszer egy KOPPINTÁSt előnyben részesített az összevonás során. Egy KOPPINTÁSsal rendelkező felhasználó végrehajtja a hitelesítést az Azure AD-ben, és nem lesz átirányítva az összevont identitás-szolgáltatóhoz (identitásszolgáltató).

A felhasználó be van jelentkezve, és frissíthet vagy regisztrálhat egy metódust, például a FIDO2 biztonsági kulcsot. Azok a felhasználók, akik frissítik hitelesítési módszereiket a hitelesítő adataik vagy eszközök elvesztése miatt, el kell végezniük a régi hitelesítési módszerek eltávolítását.

A felhasználók a TAP használatával is regisztrálhatnak a jelszó nélküli telefonos bejelentkezéshez közvetlenül a hitelesítő alkalmazásból. További információkért lásd: [munkahelyi vagy iskolai fiók hozzáadása a Microsoft Authenticator alkalmazáshoz](../user-help/user-help-auth-app-add-work-school-account.md).

![A TAP munkahelyi vagy iskolai fiókkal való megadását bemutató képernyőkép](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-tap"></a>KOPPINTÁS törlése

Lejárt KOPPINTÁS nem használható. A felhasználó **hitelesítési módszerei** alatt a **részletek** oszlopban látható, hogy a koppintás Mikor járt le. Ezek a lejárt csapok a következő lépésekkel törölhetők:

1. Az Azure AD-portálon keresse meg a **felhasználók** elemet, válasszon ki egy felhasználót, például *koppintson a felhasználó* elemre, majd válassza a **hitelesítési módszerek** lehetőséget.
1. A listában látható **ideiglenes hozzáférési pass (előzetes verzió)** hitelesítési módszer jobb oldalán válassza a **Törlés** lehetőséget.

## <a name="replace-a-tap"></a>KOPPINTÁS cseréje 

- Egy felhasználónak csak egyetlen KOPPINTÁSa lehet. A PIN-kód a KOPPINTÁS kezdési és befejezési ideje alatt használható.
- Ha a felhasználónak új KOPPINTÁSra van szüksége:
  - Ha a meglévő KOPPINTÁS érvényes, a rendszergazdának törölnie kell a meglévő KOPPINTÁSt, és létre kell hoznia egy új Pass-t a felhasználó számára. Egy érvényes KOPPINTÁS törlésével a rendszer visszavonja a felhasználó munkameneteit. 
  - Ha a meglévő KOPPINTÁS lejárt, egy új KOPPINTÁS felülbírálja a meglévő KOPPINTÁSt, és nem vonja vissza a felhasználó munkameneteit.

További információ a bevezetéshez és a helyreállításhoz szükséges NIST-szabványokról: a [NIST speciális kiadvány 800 – 63A](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Korlátozások

Tartsa szem előtt a következő korlátozásokat:

- Ha egyszeri KOPPINTÁSt használ a jelszó nélküli módszer (például a FIDO2 vagy a telefonos bejelentkezés) regisztrálásához, a felhasználónak 10 percen belül be kell fejeznie a regisztrációt az egyszeri KOPPINTÁSsal. Ez a korlátozás nem vonatkozik olyan KOPPINTÁSra, amely többször is felhasználható.
- A vendég felhasználók nem tudnak bejelentkezni egy KOPPINTÁSsal.
- Az önkiszolgáló jelszó-visszaállítás (SSPR) hatókörében lévő felhasználóknak regisztrálniuk kell a SSPR metódusok egyikét, miután bejelentkezett a KOPPINTÁS lehetőséggel. Ha a felhasználó csak a FIDO2 kulcsot fogja használni, zárja ki őket a SSPR-szabályzatból, vagy tiltsa le a SSPR regisztrációs házirendjét. 
- KOPPINTson a hálózati házirend-kiszolgáló (NPS) és a Active Directory összevonási szolgáltatások (AD FS) (AD FS) adapterrel nem használható.
- Ha a zökkenőmentes SSO engedélyezve van a bérlőn, a rendszer felszólítja a felhasználókat a jelszó megadására. Az **ideiglenes hozzáférési pass helyett** a hivatkozás elérhető lesz a felhasználó számára a TAP használatával való bejelentkezéshez.

![Képernyőkép a TAP használatával](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Hibaelhárítás    

- Ha a bejelentkezés során a TAP nem érhető el a felhasználó számára, ellenőrizze a következőket:
  - A felhasználó hatókörben van az KOPPINTó hitelesítési módszer házirendjében.
  - A felhasználó rendelkezik érvényes KOPPINTÁSsal, és ha egyszer használatos, még nem használták.
- Ha az **ideiglenes hozzáférési pass bejelentkezés le lett tiltva, mert a felhasználói hitelesítő adatok házirendje** a bejelentkezés a következővel lehetőséggel jelenik meg, akkor ellenőrizze a következőket:
  - A felhasználó több-használatú KOPPINTÁSsal rendelkezik, míg a hitelesítési módszer házirendje egyszeri KOPPINTÁSt igényel.
  - Már használatban van egy egyszeri KOPPINTÁS.

## <a name="next-steps"></a>Következő lépések

- [Jelszóval nem rendelkező hitelesítés központi telepítésének megtervezése Azure Active Directory](howto-authentication-passwordless-deployment.md)

