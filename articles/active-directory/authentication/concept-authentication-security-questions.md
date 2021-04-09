---
title: Biztonsági kérdések hitelesítési módszere – Azure Active Directory
description: További információ a Azure Active Directory biztonsági kérdéseinek használatáról a bejelentkezési események javításához és biztonságossá tételéhez
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7229dabd690e5932fbd297992e09782eda85a002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744142"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Hitelesítési módszerek a Azure Active Directory – biztonsági kérdések

A bejelentkezési esemény során a biztonsági kérdések nem használhatók hitelesítési módszerként. Ehelyett biztonsági kérdéseket használhat az önkiszolgáló jelszó-visszaállítási (SSPR) folyamat során annak megerősítéséhez, hogy ki vagy. A rendszergazdai fiókok nem használhatnak biztonsági kérdéseket ellenőrzési módszerként a SSPR.

Amikor a felhasználók regisztráljanak a SSPR, a rendszer felkéri a használandó hitelesítési módszerek kiválasztására. Ha úgy döntenek, hogy biztonsági kérdéseket használnak fel, válasszon ki egy kérdést, hogy Kérdezzen rá, majd adja meg a saját válaszait.

![A biztonsági kérdések hitelesítési módszereit és lehetőségeit megjelenítő Azure Portal képernyőképe](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> A biztonsági kérdések tárolása a címtárban található felhasználói objektumon történik, és a felhasználók csak a regisztráció során válaszolnak. Nincs mód arra, hogy a rendszergazda beolvassa vagy módosítsa a felhasználó kérdéseit és válaszait.

A biztonsági kérdések kevésbé biztonságosak, mint más metódusok, mert egyes felhasználók tudják, hogy a válaszokat egy másik felhasználó kérdéseire. Ha a SSPR biztonsági kérdéseit használja, azt javasoljuk, hogy egy másik módszerrel együtt használja őket. A felhasználó kérheti a Microsoft Authenticator alkalmazás vagy a telefonos hitelesítés használatát a SSPR folyamat során a személyazonosságának ellenőrzéséhez, és csak akkor válassza a biztonsági kérdéseket, ha azok nem rendelkeznek telefonos vagy regisztrált eszközzel.

## <a name="predefined-questions"></a>Előre meghatározott kérdések

A következő előre meghatározott biztonsági kérdések használhatók ellenőrzési módszerként a SSPR. Az összes ilyen biztonsági kérdés fordítása és honosítása a felhasználó böngésző területi beállítása alapján a Microsoft 365 nyelvének teljes készletében történik:

* Melyik városban teljesítette az első házastársa/partnerét?
* Milyen városokban felelt meg a szülei?
* Melyik városban él a legközelebbi testvér?
* Melyik városban született az apja?
* Melyik városban volt az első feladata?
* Melyik városban született az anyja?
* Mi a város a New Year 's 2000-ben?
* Mi a vezetékneve kedvenc tanárának a középiskolában?
* Mi a neve annak a Főiskolának, amelyet alkalmazott, de nem vett részt?
* Mi a neve annak a helynek, amelyben az első esküvői fogadást megtartotta?
* Mi az apád középső neve?
* Mi a kedvenc étele?
* Mi az anyai nagymama első és vezetékneve?
* Mi az anyja középső neve?
* Mi a legrégebbi testvére születésnapjának hónapja és éve? (például 1985 november)
* Mi a legrégebbi testvérének középső neve?
* Apai nagyapa vezetékneve és keresztneve?
* Mi a legfiatalabb testvér középső neve?
* Milyen iskolát vett részt a hatodik osztályban?
* Mi volt az első és az utolsó név a gyermekkori legjobb barátodnak?
* Mi volt az első és az utolsó név, ami az első jelentős?
* Mi volt a vezetékneve kedvenc iskolai tanárának?
* Mi volt az első autójának vagy motorkerékpárjának gyártmánya és modellje?
* Mi volt az első iskolájának neve?
* Mi volt a kórház neve, amelyben született?
* Mi volt az első gyermekkori kezdőlapjának neve?
* Mi volt a gyermekkori hős neve?
* Mi volt a kedvenc töltött állatának neve?
* Mi volt az első kisállat neve?
* Mi volt a gyerekkori beceneve?
* Mi volt a kedvenc sportja a középiskolában?
* Mi volt az első feladata?
* Mi volt a gyermekkori telefonszámának utolsó négy számjegye?
* Ha fiatal volt, mit szeretne, amikor felnőtt?
* Ki a leghíresebb személy, akivel valaha is találkozott?

## <a name="custom-security-questions"></a>Egyéni biztonsági kérdések

A további rugalmasság érdekében megadhatja saját egyéni biztonsági kérdéseit. Az egyéni biztonsági kérdések maximális hossza 200 karakter.

Az egyéni biztonsági kérdések nincsenek automatikusan honosítva, mint az alapértelmezett biztonsági kérdések. Minden egyéni kérdés a rendszergazda felhasználói felületen megadott nyelven jelenik meg, még akkor is, ha a felhasználó böngésző területi beállítása eltér. Ha honosított kérdésekre van szüksége, használja az előre meghatározott kérdéseket.

## <a name="security-question-requirements"></a>Biztonsági kérdésekkel kapcsolatos követelmények

Az alapértelmezett és az egyéni biztonsági kérdések esetében a következő követelmények és korlátozások érvényesek:

* A minimális válasz karakteres korlát három karakterből állhat.
* A maximális válasz karakteres korlátja 40 karakter.
* A felhasználók több alkalommal nem tudnak válaszolni ugyanarra a kérdésre.
* A felhasználók nem tudnak ugyanazt a választ adni több kérdéshez.
* Bármely karakterkészlet használható a kérdések és válaszok meghatározására, beleértve a Unicode karaktereket is.
* A megadott kérdések számának nagyobbnak vagy egyenlőnek kell lennie a regisztráláshoz szükséges kérdések számával.

## <a name="next-steps"></a>Következő lépések

Első lépésként tekintse meg az önkiszolgáló [jelszó-visszaállítás (SSPR) című oktatóanyagot][tutorial-sspr].

További információ a SSPR fogalmakról: az [Azure ad önkiszolgáló jelszó-visszaállításának működése][concept-sspr].

További információ a hitelesítési módszerek konfigurálásáról a [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)használatával.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
