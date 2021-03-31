---
title: Hol található a további ellenőrzési oldal? – Azure AD
description: A további biztonsági ellenőrzés oldalának beszerzése a kétfaktoros ellenőrzéshez
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e5b07f8f7ae766d110c87a495a3e1623b815e526
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88795983"
---
# <a name="what-is-the-additional-verification-page"></a>Hol található a további ellenőrzési oldal?

A szervezete további lépéseket tesz annak biztosítására, hogy Ön mit mond, amikor Ön bejelentkezik. Ezt az extra biztonsági ellenőrzést más néven kétfaktoros ellenőrzésnek is nevezzük. Ez a Felhasználónév, a jelszó és a mobileszköz, illetve a telefon kombinációjából áll. Ha az összeset szeretné elvégezni, kapcsolja ki a kétfaktoros ellenőrzést a Microsoft-fiók például a alain@outlook.com következő témakör utasításait: a [kétfaktoros ellenőrzés bekapcsolása a Microsoft-fiók](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

<center>

![Fogalmi hitelesítési módszerek képe](../authentication/media/concept-mfa-howitworks/methods.png)</center>

A kétfaktoros ellenőrzés biztonságosabb, mint a jelszó, mivel a hitelesítés két formájára támaszkodik:

- Amit ismer, például a jelszavát.
- Egy dolog, például telefon vagy más eszköz, amelyet Ön is végez.

A kétfaktoros ellenőrzés segít megakadályozni, hogy a rosszindulatú hackerek az Ön számára is kiálljanak. Akkor is, ha a jelszavuk is van, az az esélye, hogy nem rendelkezik az eszközzel.

>[!Important]
>Ha Ön rendszergazda, aki az alkalmazottak vagy más felhasználók kétfaktoros ellenőrzésének bekapcsolásával kapcsolatos információkat keres, tekintse meg a [Azure Active Directory hitelesítési dokumentációját](../authentication/index.yml). Ez a cikk arra szolgál, hogy a felhasználók a kéttényezős ellenőrzést használják munkahelyi vagy iskolai fiókkal (például alain@contoso.com ).

## <a name="who-decides-if-you-use-this-feature"></a>Ki dönti el, hogy használja-e ezt a funkciót?

Ki dönti el, hogy kétfaktoros ellenőrzést használ-e, attól függ, hogy milyen típusú fiókkal rendelkezik:

- **Munkahelyi vagy iskolai fiók.** Ha munkahelyi vagy iskolai fiókot (például alain@contoso.com ) használ, akkor a szervezet akár a kétfaktoros ellenőrzés, akár a konkrét ellenőrzési módszerek használatával is felhasználható. Mivel a szervezet úgy döntött, hogy ezt a funkciót kell használnia, nincs mód arra, hogy önállóan kikapcsolja.

- **Személyes Microsoft-fiók.** Megadhatja, hogy a személyes Microsoft-fiókjaihoz (például) Kéttényezős ellenőrzést állítson be alain@outlook.com . Bármikor be-és kikapcsolhatja a [kétfaktoros ellenőrzés bekapcsolásához szükséges](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)egyszerű utasításokat a Microsoft-fiók.

    >[!Note]
    >Ha más problémákba ütközik a kétfaktoros ellenőrzés és az egyik személyes Microsoft-fiókja között, akkor a [kétlépéses ellenőrzésnek a Microsoft-fiók használatával történő használatának](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification)további javaslatai vannak.

## <a name="open-the-additional-security-verification-page"></a>A további biztonsági ellenőrzés lap megnyitása

Miután a szervezet bekapcsolta a kétfaktoros ellenőrzést, minden bejelentkezéskor megjelenik egy üzenet, amelyből megtudhatja, hogy a fiókja biztonsága érdekében további információkhoz juthat.

![További információ kérése szükséges](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>A további biztonsági ellenőrzés oldalának elérése

1. Kattintson a **Tovább gombra** a **További információk** megadásához.

    Megjelenik a **további biztonsági ellenőrzés** oldal.

2. A **további biztonsági ellenőrzés** lapon válassza ki a kétfaktoros ellenőrzési módszert, amelyet annak ellenőrzésére használ, hogy a felhasználó a munkahelyi vagy iskolai fiókjába jelentkezik be. Az alábbiak közül választhat:

    | Kapcsolatfelvételi mód | Description |
    | --- | --- |
    | Mobilalkalmazás | <ul><li>**Értesítések fogadása az ellenőrzéshez.** Ez a beállítás egy értesítést küld az okostelefonján vagy a táblaszámítógépen lévő hitelesítő alkalmazásnak. Tekintse meg az értesítést, és ha az megbízható, válassza a **hitelesítés** lehetőséget az alkalmazásban. Előfordulhat, hogy a munkahelye vagy iskolája megköveteli, hogy a hitelesítés előtt PIN-kódot adjon meg.</li><li>**Ellenőrző kód használata.** Ebben a módban az alkalmazás egy 30 másodpercenként frissülő ellenőrző kódot hoz létre. Adja meg a jelenlegi ellenőrző kódot a bejelentkezési képernyőn.<br>Az Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?linkid=866594) és [iOS](https://go.microsoft.com/fwlink/?linkid=866594)rendszerhez érhető el.</li></ul> |
    | Hitelesítő telefon | <ul><li>A **telefonhívás** egy automatikus hanghívást helyez el az Ön által megadott telefonszámra. Válaszolja meg a hívást, majd a hitelesítéshez nyomja meg a telefon billentyűzetén a font (#) gombot.</li><li>A **szöveges üzenet** egy ellenőrző kódot tartalmazó szöveges üzenetet ér véget. A szövegben szereplő Rákérdezés után válaszoljon a szöveges üzenetre, vagy adja meg a bejelentkezési felületen megadott ellenőrző kódot.</li></ul> |
    | Munkahelyi telefon | Elhelyez egy automatikus hanghívást az Ön által megadott telefonszámon. Válaszolja meg a hívást, majd a hitelesítéshez nyomja meg a telefon billentyűzetén a font (#) gombot. |

## <a name="next-steps"></a>Következő lépések

Miután kiválasztott egy kétfaktoros ellenőrzési módszert a **további biztonsági ellenőrzés** oldalon, be kell állítania a következőket:

- [Mobileszköz beállítása ellenőrzési módszerként](multi-factor-authentication-setup-phone-number.md)

- [Az irodai telefon beállítása ellenőrzési módszerként](multi-factor-authentication-setup-office-phone.md)

- [A Microsoft Authenticator alkalmazás beállítása ellenőrzési módszerként](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

- [Bejelentkezés kétfaktoros ellenőrzés használatával](multi-factor-authentication-end-user-signin.md)

- [Segítség kérése kétfaktoros ellenőrzéssel](multi-factor-authentication-end-user-troubleshoot.md)