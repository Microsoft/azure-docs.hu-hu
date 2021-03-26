---
title: Ideiglenes hozzáférési pass beállítása az Azure AD-ben a jelszóval nem rendelkező hitelesítési módszerek regisztrálásához
description: Megtudhatja, hogyan konfigurálhatja és engedélyezheti a felhasználók számára a jelszavas hitelesítési módszerek regisztrálását ideiglenes hozzáférési pass használatával
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0805ac84318a4fee98c30127ac80c0dac2b96309
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558261"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Ideiglenes hozzáférési pass beállítása az Azure AD-ben a jelszóval nem rendelkező hitelesítési módszerek regisztrálásához (előzetes verzió)

A jelszó nélküli hitelesítési módszerek, például a FIDO2 és a jelszó nélküli telefonos bejelentkezés a Microsoft Authenticator alkalmazáson keresztül, lehetővé teszi a felhasználók számára, hogy jelszó nélkül biztonságosan jelentkezzenek be. A felhasználók a jelszóval nem rendelkező metódusok kétféleképpen jelentkezhetnek be:

- Meglévő Azure AD-Multi-Factor Authentication metódusok használata 
- Ideiglenes hozzáférési pass használata (KOPPINTÁS) 

Az ideiglenes hozzáférési pass egy olyan időkorlátos PIN-kód, amelyet egy olyan rendszergazda állít ki, amely megfelel az erős hitelesítési követelményeknek, és használható más hitelesítési módszerek bevezetésére, beleértve a jelszó nélkülieket is. Az ideiglenes hozzáférési pass is megkönnyíti a helyreállítást, ha egy felhasználó elvesztette vagy elfelejtette az erős hitelesítési tényezőt, például egy FIDO2 biztonsági kulcsot vagy Microsoft Authenticator alkalmazást, de be kell jelentkeznie az új erős hitelesítési módszerek regisztrálásához.


Ebből a cikkből megtudhatja, hogyan engedélyezheti és használhatja az Azure AD-vel való ideiglenes hozzáférési átadást az Azure Portal használatával. Ezeket a műveleteket a REST API-k használatával is végrehajthatja. 

>[!NOTE]
>Az ideiglenes hozzáférési pass jelenleg nyilvános előzetes verzióban érhető el. Előfordulhat, hogy egyes funkciók nem támogatottak vagy korlátozott képességekkel rendelkeznek. 

## <a name="enable-the-temporary-access-pass-policy"></a>Ideiglenes hozzáférés-továbbítási szabályzat engedélyezése

Az ideiglenes hozzáférés-átadási szabályzat meghatározza a beállításokat, például a bérlőn létrehozott hágók élettartamát, illetve azokat a felhasználókat és csoportokat, akik ideiglenes hozzáférési Pass-t használhatnak a bejelentkezéshez. Mielőtt bárki bejelentkezhet egy ideiglenes hozzáférési lépéssel, engedélyeznie kell a hitelesítési módszer házirendjét, és ki kell választania, hogy mely felhasználók és csoportok jelentkezhetnek be egy ideiglenes hozzáférési menet használatával.
Habár bármely felhasználó számára ideiglenes hozzáférési Pass-t hozhat létre, csak a szabályzatban szereplő felhasználók jelentkezhetnek be.

A globális rendszergazda és a hitelesítési módszer házirend-rendszergazdai szerepkör birtokosai frissíthetik az ideiglenes hozzáférési pass hitelesítési módszer házirendjét.
Az ideiglenes hozzáférési pass hitelesítési módszer házirendjének konfigurálása:

1. Jelentkezzen be a Azure Portal globális rendszergazdaként, és kattintson a **Azure Active Directory**  >  **biztonsági**  >  **hitelesítési módszerek**  >  **ideiglenes hozzáférési pass** elemre.
1. Az **Igen** gombra kattintva engedélyezheti a szabályzatot, kiválaszthatja, hogy mely felhasználók rendelkezzenek az alkalmazott szabályzattal, valamint az **általános** beállításokkal.

   ![Képernyőkép az ideiglenes hozzáférési pass hitelesítési módszer házirendjének engedélyezéséről](./media/how-to-authentication-temporary-access-pass/policy.png)

   Az alapértelmezett értéket és az engedélyezett értékek tartományát az alábbi táblázat ismerteti.


   | Beállítás | Alapértelmezett értékek | Megengedett értékek | Megjegyzések |
   |---|---|---|---|
   | Minimális élettartam | 1 óra | 10 – 43200 perc (30 nap) | Az ideiglenes hozzáférési továbbítás érvényes minimális száma percben. |
   | Maximális élettartam | 24 óra | 10 – 43200 perc (30 nap) | Az ideiglenes hozzáférési továbbítás érvényességi idejének maximális száma percben. |
   | Alapértelmezett élettartam | 1 óra | 10 – 43200 perc (30 nap) | Az alapértelmezett értékeket felülbírálhatja az egyes fázisok, a házirend által beállított minimális és maximális élettartamon belül. |
   | Egyszeri használat | Hamis | Igaz/hamis | Ha a házirend hamis értékre van állítva, akkor a bérlőben lévő átadások az érvényessége során egyszer vagy többször is használhatók (maximális élettartam). Ha az ideiglenes hozzáférési szabályzatban egyszeri használatot alkalmaz, a bérlőben létrehozott összes hágó egyszeri használatra lesz létrehozva. |
   | Hossz | 8 | 8-48 karakter | Meghatározza a PIN-kód hosszát. |

## <a name="create-a-temporary-access-pass-in-the-azure-ad-portal"></a>Ideiglenes hozzáférési menet létrehozása az Azure AD-portálon

Miután engedélyezte a szabályzatot, létrehozhat egy ideiglenes hozzáférési Pass-t egy felhasználó számára az Azure AD-ben. Ezek a szerepkörök a következő műveleteket hajtják végre egy ideiglenes hozzáférési menethez kapcsolódóan.

- A globális rendszergazda létrehozhat, törölhet, megtekintheti az ideiglenes hozzáférési átadást bármely felhasználónál (kivéve magukat)
- A privilegizált hitelesítő rendszergazdák létrehozhatnak, törölhetnek és megtekinthetnek egy ideiglenes hozzáférési Pass-t a rendszergazdák és a tagok számára (kivéve magukat)
- A hitelesítési rendszergazdák létrehozhatnak, törölhetnek, megtekinthetnek egy ideiglenes hozzáférési átadást a tagokon (kivéve magukat)
- A globális rendszergazda megtekintheti az ideiglenes hozzáférési adatokat a felhasználónál (a kód beolvasása nélkül).

Ideiglenes hozzáférési pass létrehozásához:

1. Jelentkezzen be a portálra globális rendszergazdaként, privilegizált hitelesítési rendszergazdaként vagy hitelesítési rendszergazdaként. 
1. Kattintson a **Azure Active Directory** gombra, tallózással keresse meg a felhasználók elemet, válasszon ki egy felhasználót, például: *Chris Green*, majd válassza a **hitelesítési módszerek** lehetőséget.
1. Ha szükséges, válassza az **új felhasználói hitelesítési módszerek kipróbálása** lehetőséget.
1. Válassza a **hitelesítési módszerek hozzáadásának** lehetőségét.
1. Válassza az alábbi **módszer lehetőséget**, majd kattintson az **ideiglenes hozzáférési pass (előzetes verzió)** lehetőségre.
1. Definiáljon egyéni aktiválási időt vagy időtartamot, majd kattintson a **Hozzáadás** gombra.

   ![Az ideiglenes hozzáférési pass létrehozásának képernyőképe](./media/how-to-authentication-temporary-access-pass/create.png)

1. A Hozzáadás után a rendszer megjeleníti az ideiglenes hozzáférési menet részleteit. Jegyezze fel a tényleges ideiglenes hozzáférési pass értéket. Ezt az értéket a felhasználónak kell megadnia. Ezt az értéket nem tekintheti meg, miután rákattintott **az OK** gombra.
   
   ![Képernyőkép az ideiglenes hozzáférési pass részleteiről](./media/how-to-authentication-temporary-access-pass/details.png)

## <a name="use-a-temporary-access-pass"></a>Ideiglenes hozzáférési pass használata

Az ideiglenes hozzáférési menetek leggyakoribb felhasználási célja, hogy a felhasználók az első bejelentkezéskor regisztrálják a hitelesítési adatokat, anélkül, hogy további biztonsági kérdéseket kellene elvégezniük. A hitelesítési módszerek regisztrálva vannak a következő helyen: [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . A felhasználók itt is frissíthetik a meglévő hitelesítési módszereket.

1. Nyisson meg egy webböngészőt a következőhöz: [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Adja meg annak a fióknak az egyszerű felhasználónevét, amelyhez az ideiglenes hozzáférési átadást hozta létre, például: *tapuser@contoso.com* .
1. Ha a felhasználó beletartozik az ideiglenes hozzáférési szabályzatba, megjelenik egy képernyő, amelyen megadhatja az ideiglenes hozzáférési menetét.
1. Adja meg a Azure Portalban megjelenő ideiglenes hozzáférési pass értéket.

   ![Az ideiglenes hozzáférési pass beírásának képernyőképe](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>Az összevont tartományok esetében az összevonás előnyben részesített egy ideiglenes hozzáférési pass. Az ideiglenes hozzáféréssel rendelkező felhasználók elvégzik a hitelesítést az Azure AD-ben, és nem lesznek átirányítva az összevont identitás-szolgáltatóhoz (identitásszolgáltató).

A felhasználó be van jelentkezve, és frissíthet vagy regisztrálhat egy metódust, például a FIDO2 biztonsági kulcsot. Azok a felhasználók, akik frissítik hitelesítési módszereiket a hitelesítő adataik vagy eszközök elvesztése miatt, el kell végezniük a régi hitelesítési módszerek eltávolítását.

A felhasználók az ideiglenes hozzáférési hozzáféréssel is használhatják a jelszó nélküli telefonos bejelentkezést közvetlenül a hitelesítő alkalmazásból. További információkért lásd: [munkahelyi vagy iskolai fiók hozzáadása a Microsoft Authenticator alkalmazáshoz](../user-help/user-help-auth-app-add-work-school-account.md).

![Képernyőkép a munkahelyi vagy iskolai fiókkal való ideiglenes hozzáférési menet megadásáról](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-temporary-access-pass"></a>Ideiglenes hozzáférési pass törlése

Lejárt ideiglenes hozzáférési pass nem használható. A felhasználó **hitelesítési módszerei** alatt a **részletek** oszlopban látható, hogy az ideiglenes hozzáférési menet lejárt-e. A lejárt ideiglenes hozzáférési lépéseket a következő lépésekkel törölheti:

1. Az Azure AD-portálon keresse meg a **felhasználók** elemet, válasszon ki egy felhasználót, például *koppintson a felhasználó* elemre, majd válassza a **hitelesítési módszerek** lehetőséget.
1. A listában látható **ideiglenes hozzáférési pass (előzetes verzió)** hitelesítési módszer jobb oldalán válassza a **Törlés** lehetőséget.

## <a name="replace-a-temporary-access-pass"></a>Ideiglenes hozzáférési pass cseréje 

- Egy felhasználó csak egy ideiglenes hozzáférési Pass-t tartalmazhat. A PIN-kód az ideiglenes hozzáférési menet kezdési és befejezési ideje alatt használható.
- Ha a felhasználónak új ideiglenes hozzáférési pass-re van szüksége:
  - Ha a meglévő ideiglenes hozzáférési pass érvényes, a rendszergazdának törölnie kell a meglévő ideiglenes hozzáférési Passet, és létre kell hoznia egy új Pass-t a felhasználó számára. Egy érvényes ideiglenes hozzáférési pass törlésével a rendszer visszavonja a felhasználó munkameneteit. 
  - Ha a meglévő ideiglenes hozzáférési pass lejárt, akkor egy új ideiglenes hozzáférési pass felülbírálja a meglévő ideiglenes hozzáférési átadást, és nem vonja vissza a felhasználó munkameneteit.

További információ a bevezetéshez és a helyreállításhoz szükséges NIST-szabványokról: a [NIST speciális kiadvány 800 – 63A](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Korlátozások

Tartsa szem előtt a következő korlátozásokat:

- Ha egyszer használatos ideiglenes hozzáférési Pass-t használ a jelszó nélküli módszer (például a FIDO2 vagy a telefonos bejelentkezés) regisztrálásához, a felhasználónak 10 percen belül be kell fejeznie a regisztrációt az egyszeri ideiglenes hozzáférési lépéssel. Ez a korlátozás nem vonatkozik olyan ideiglenes hozzáférési Passre, amely többször is felhasználható.
- A vendég felhasználók nem jelentkezhetnek be ideiglenes hozzáférési pass-be.
- Az önkiszolgáló jelszó-visszaállítás (SSPR) hatókörében lévő felhasználóknak regisztrálniuk kell a SSPR metódusok egyikét, miután bejelentkezett egy ideiglenes hozzáférési lépéssel. Ha a felhasználó csak a FIDO2 kulcsot fogja használni, zárja ki őket a SSPR-szabályzatból, vagy tiltsa le a SSPR regisztrációs házirendjét. 
- Ideiglenes hozzáférési pass nem használható a hálózati házirend-kiszolgáló (NPS) bővítmény és Active Directory összevonási szolgáltatások (AD FS) (AD FS) adapterrel.
- Ha a zökkenőmentes SSO engedélyezve van a bérlőn, a rendszer felszólítja a felhasználókat a jelszó megadására. Az **ideiglenes hozzáférési pass helyett** a hivatkozás elérhető lesz a felhasználó számára, hogy egy ideiglenes hozzáférési menetben jelentkezzen be.

  ![Képernyőkép az ideiglenes hozzáférési pass használatáról](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Hibaelhárítás    

- Ha a bejelentkezés során a rendszer nem biztosít ideiglenes hozzáférési továbbítást a felhasználónak, ellenőrizze a következőket:
  - A felhasználó az ideiglenes hozzáférési pass hitelesítési módszer házirendjének hatókörében van.
  - A felhasználónak érvényes ideiglenes hozzáférési pass-nek kell lennie, és ha egyszer használatba van, még nem használták.
- Ha az **ideiglenes hozzáférési pass bejelentkezést a rendszer a felhasználói hitelesítő adatok házirendje miatt letiltotta** , akkor a bejelentkezéskor a következőt kell ellenőriznie:
  - A felhasználónak van egy többszörös használatú ideiglenes hozzáférési lépése, miközben a hitelesítési módszer házirendjéhez egyszeri, ideiglenes hozzáférési pass szükséges.
  - Már használatban van egy egyszeri ideiglenes hozzáférési pass.

## <a name="next-steps"></a>Következő lépések

- [Jelszóval nem rendelkező hitelesítés központi telepítésének megtervezése Azure Active Directory](howto-authentication-passwordless-deployment.md)

