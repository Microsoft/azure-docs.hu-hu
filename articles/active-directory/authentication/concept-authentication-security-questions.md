---
title: Biztonsági kérdések hitelesítési módszere – Azure Active Directory
description: A bejelentkezési események javításának és Azure Active Directory kapcsolatos biztonsági kérdések használatának megismerése
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 841391778e0fb8c00f503aa0cc79b5562661e309
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530350"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Hitelesítési módszerek a Azure Active Directory – biztonsági kérdések

A biztonsági kérdések nem használhatók hitelesítési módszerként a bejelentkezési események során. Ehelyett biztonsági kérdések használhatók az önkiszolgáló jelszó-visszaállítási (SSPR) folyamat során annak megerősítéséhez, hogy ki Ön. A rendszergazdai fiókok nem használhatnak biztonsági kérdéseket ellenőrzési módszerként az SSPR-hez.

Amikor a felhasználók regisztrálnak az SSPR-re, a rendszer felkéri őket, hogy válasszanak hitelesítési módszereket. Ha a biztonsági kérdések használatát választják, több kérdés közül választ, amelyekre rákérdeznek, majd meg is biztosítanak saját válaszokat.

![A hitelesítési módszereket Azure Portal biztonsági kérdésekre vonatkozó lehetőségeket bemutató képernyőkép](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> A biztonsági kérdéseket a rendszer privát módon és biztonságosan tárolja a címtár egy felhasználói objektumán, és csak a regisztráció során válaszolhatja meg. A rendszergazdák nem olvashatják vagy módosíthatják a felhasználók kérdéseit és válaszait.

A biztonsági kérdések kevésbé biztonságosak, mint a többi módszer, mert előfordulhat, hogy vannak, akik ismerik egy másik felhasználó kérdéseire adott válaszokat. Ha biztonsági kérdéseket használ az SSPR-hez, javasoljuk, hogy ezeket egy másik módszerrel együtt használja. A rendszer arra kéri a felhasználót, hogy az SSPR-folyamat során az Microsoft Authenticator-alkalmazással vagy telefonos hitelesítéssel ellenőrizze identitását, és csak akkor válasszon biztonsági kérdéseket, ha nincs telefonja vagy regisztrált eszköze.

## <a name="predefined-questions"></a>Előre meghatározott kérdések

Az alábbi előre meghatározott biztonsági kérdések érhetők el ellenőrzési módszerként az SSPR-hez. Ezeket a biztonsági kérdéseket a rendszer lefordítja és honosította Microsoft 365 nyelvek teljes halmazára a felhasználó böngészőjének területi beállításai alapján:

* Melyik városban ismerkedtek meg az első férjével/partnerével?
* Melyik városban ismerkedtek meg a szülők?
* Melyik városban él a legközelebbi testvér?
* Melyik városban született az apa?
* Melyik városban volt az első feladata?
* Melyik városban született az anya?
* Melyik városban volt new year's 2000-ben?
* Mi a kedvenc tanárának vezetékneve a high schoolban?
* Mi a neve annak az egyetemnek, amelybe ön jelentkezett, de nem járt ott?
* Mi a neve annak a helynek, ahol az első fogadást tartott?
* Mi az apa középső neve?
* Mi a kedvenc étele?
* Mi az anyák vezetékneve és vezetékneve?
* Mi az anya középső neve?
* Mi a legidősebb testvérének születésnapja hónap és év? (például 1985. november)
* Mi a legrégebbi testvér középső neve?
* Mi a személyes foga vezetékneve és vezetékneve?
* Mi a testvér középső neve?
* Melyik iskolán részt vesz hatodik osztályban?
* Mi volt a legjobb barátjának a vezeték- és vezetékneve?
* Mi volt az első neve és vezetékneve társ?
* Mi volt a kedvenc tanárának vezetékneve?
* Milyen volt az első autója vagy motorja, és milyen a modellje?
* Mi volt az első iskolája neve, amit először látogatott?
* Mi volt annak a kórháznak a neve, amelyben született?
* Mi volt az első otthona utcaneve?
* Mi volt a főképének a neve?
* Mi volt a kedvenc megtömött állatja neve?
* Mi volt az első háziállata neve?
* Mi volt a kedvenc beceneve?
* Mi volt a kedvenc sportja a high schoolban?
* Mi volt az első feladata?
* Mi volt a mobiltelefonszámának utolsó négy számjegye?
* Milyen szeretne lenni, amikor fiatalon nőtt fel?
* Ki a legismertebb személy, akivel valaha is találkozott?

## <a name="custom-security-questions"></a>Egyéni biztonsági kérdések

A további rugalmasság érdekében saját egyéni biztonsági kérdéseket is meghatározhat. Az egyéni biztonsági kérdések maximális hossza 200 karakter.

Az egyéni biztonsági kérdések nem lokalizálódnak automatikusan, mint az alapértelmezett biztonsági kérdések esetén. Minden egyéni kérdés ugyanazon a nyelven jelenik meg, mint a rendszergazdai felhasználói felületen, még akkor is, ha a felhasználó böngészőjének területi beállításai eltérnek. Ha honosított kérdésekre van szüksége, használja az előre meghatározott kérdéseket.

## <a name="security-question-requirements"></a>Biztonsági kérdésekre vonatkozó követelmények

Az alapértelmezett és egyéni biztonsági kérdésekre a következő követelmények és korlátozások vonatkoznak:

* A válasz karakterkorlátja legfeljebb három karakter lehet.
* A válasz karakterkorlátja legfeljebb 40 karakter lehet.
* A felhasználók nem válaszolják meg egynél több alkalommal ugyanazt a kérdést.
* A felhasználók nem tudnak ugyanazt a választ adni egynél több kérdésre.
* Bármilyen karakterkészlet használható a kérdések és válaszok meghatározására, beleértve a Unicode-karaktereket is.
* A meghatározott kérdések számának nagyobbnak vagy egyenlőnek kell lennie a regisztrációhoz szükséges kérdések számában.

## <a name="next-steps"></a>Következő lépések

Első lépésekért tekintse meg az önkiszolgáló [jelszó-visszaállítás (SSPR) oktatóanyagát.][tutorial-sspr]

További információ az SSPR fogalmairól: [Az Azure AD önkiszolgáló jelszóát állításának működése.][concept-sspr]

További információ a hitelesítési módszerek konfigurálásról a [Microsoft Graph REST API.](/graph/api/resources/authenticationmethods-overview)

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
