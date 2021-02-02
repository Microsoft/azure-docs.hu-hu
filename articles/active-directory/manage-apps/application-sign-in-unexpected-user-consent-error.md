---
title: Váratlan hiba történt az alkalmazáshoz való beleegyező művelet végrehajtásakor | Microsoft Docs
description: Az alkalmazáshoz való hozzájárulás és a velük kapcsolatos teendők elvégzése során felmerülő hibák ismertetése
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f829672f88ea848e4611000b54d9cc200bc166d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259977"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Váratlan hiba történt az alkalmazáshoz való beleegyezett művelet végrehajtásakor

Ez a cikk azokat a hibákat ismerteti, amelyek az alkalmazáshoz való hozzájárulás során fordulhatnak elő. Ha olyan váratlan beleegyezés-kéréseket észlel, amelyek nem tartalmaznak hibaüzeneteket, tekintse meg [Az Azure ad hitelesítési forgatókönyveit](../develop/authentication-vs-authorization.md).

Számos olyan alkalmazás, amely integrálva van Azure Active Directory a működéséhez más erőforrásokhoz való hozzáférésre van szükség. Ha ezek az erőforrások integrálva vannak a Azure Active Directoryba, a hozzáféréshez szükséges engedélyek gyakran a közös engedélyezési keretrendszer használatával is kérhetők. Megjelenik egy hozzájárulási kérdés, amely általában az alkalmazás első használatakor fordul elő, de az alkalmazás egy későbbi használatában is előfordulhat.

Bizonyos feltételeknek igaznak kell lennie ahhoz, hogy egy felhasználó beleegyezik az alkalmazáshoz szükséges engedélyekkel. Ha ezek a feltételek nem teljesülnek, a következő hibák léphetnek fel.

## <a name="requesting-not-authorized-permissions-error"></a>Nem engedélyezett engedélyeket kérő hiba
* **AADSTS90093:** &lt; a clientAppDisplayName &gt; egy vagy több olyan engedélyt kér, amelynek Ön nem jogosult a jóváhagyásra. Forduljon a rendszergazdához, aki beleegyezik az alkalmazásba az Ön nevében.
* **AADSTS90094:** &lt; &gt; a clientAppDisplayName engedélyre van szüksége a szervezet erőforrásaihoz való hozzáféréshez, csak a rendszergazda adhat meg. Kérjen engedélyt a rendszergazdától az alkalmazáshoz, hogy használhassa azt.

Ez a hiba akkor fordul elő, ha egy olyan felhasználó, aki nem globális rendszergazda, olyan alkalmazást próbál meg használni, amely csak a rendszergazda által biztosított engedélyeket kér. Ezt a hibát feloldható egy rendszergazda, aki a szervezet nevében hozzáférést biztosít az alkalmazáshoz.

Ez a hiba akkor is előfordulhat, ha a felhasználók nem tudnak beleegyezést adni egy alkalmazásba, mert a Microsoft észleli, hogy az engedélyek iránti kérelem kockázatos. Ebben az esetben a rendszer naplózási eseményt is naplóz a "ApplicationManagement" kategóriába, a "beleegyezés az alkalmazásba" és a "kockázatos alkalmazás észlelése" állapot miatt.

Egy másik forgatókönyv, amelyben ez a hiba akkor fordulhat elő, ha a felhasználó-hozzárendelés szükséges az alkalmazáshoz, de nem adtak meg rendszergazdai jogosultságot. Ebben az esetben a rendszergazdának először meg kell adnia a rendszergazdai engedélyt.   

## <a name="policy-prevents-granting-permissions-error"></a>A szabályzat megakadályozza az engedélyek megadását
* **AADSTS90093:** A tenantDisplayName rendszergazdája &lt; olyan &gt; szabályzatot állított be, amely megakadályozza az &lt; alkalmazás nevének megadását &gt; a kért engedélyek alapján. Forduljon a &lt; tenantDisplayName rendszergazdájához &gt; , aki az Ön nevében engedélyeket adhat az alkalmazásnak.

Ez a hiba akkor fordul elő, ha a globális rendszergazda kikapcsolja a felhasználók által az alkalmazásokhoz való hozzáférés lehetőségét, és a nem rendszergazda felhasználó olyan alkalmazást próbál használni, amely beleegyezik. Ezt a hibát feloldható egy rendszergazda, aki a szervezet nevében hozzáférést biztosít az alkalmazáshoz.

## <a name="intermittent-problem-error"></a>Átmeneti hiba történt
* **AADSTS90090:** Úgy tűnik, hogy a bejelentkezési folyamat időszakos hibát észlelt, és rögzíti a clientAppDisplayName megadására megkísérelt engedélyeket &lt; &gt; . próbálkozzon újra később.

Ez a hiba azt jelzi, hogy egy időszakos szolgáltatási oldali probléma történt. Az alkalmazás újbóli beleegyezésével oldható fel.

## <a name="resource-not-available-error"></a>Az erőforrás nem érhető el. hiba
* **AADSTS65005:** Az alkalmazás &lt; clientAppDisplayName a &gt; nem elérhető erőforrás-resourceAppDisplayName eléréséhez szükséges engedélyeket &lt; &gt; . 

Lépjen kapcsolatba az alkalmazás fejlesztőjével.

##  <a name="resource-not-available-in-tenant-error"></a>Az erőforrás nem érhető el a bérlői hiba esetén
* **AADSTS65005:** &lt; a clientAppDisplayName olyan &gt; erőforrás-resourceAppDisplayName kér hozzáférést, &lt; &gt; amely nem érhető el a szervezet &lt; tenantDisplayName &gt; . 

Győződjön meg arról, hogy ez az erőforrás elérhető, vagy forduljon a tenantDisplayName rendszergazdájához &lt; &gt; .

## <a name="permissions-mismatch-error"></a>Nem megfelelő engedélyek – hiba
* **AADSTS65005:** Az alkalmazás beleegyezett az erőforrás-resourceAppDisplayName való hozzáférésre &lt; &gt; . Ez a kérelem nem sikerült, mert nem egyezik meg azzal, hogy az alkalmazás hogyan lett előre konfigurálva az alkalmazás regisztrációja során. Forduljon az alkalmazás forgalmazójához. * *

Ezek a hibák akkor fordulnak elő, amikor az alkalmazás a felhasználó beleegyezését kéri, hogy engedélyt kér egy olyan erőforrás-alkalmazás elérésére, amely nem található a szervezet címtárában (bérlője). Ez a helyzet több okból is bekövetkezhet:

-   Az ügyfélalkalmazás fejlesztője helytelenül konfigurálta az alkalmazást, így érvénytelen erőforráshoz való hozzáférést kér. Ebben az esetben az alkalmazás fejlesztőinek frissíteniük kell az ügyfélalkalmazás konfigurációját a probléma megoldásához.

-   A célként megadott erőforrás-alkalmazást képviselő egyszerű szolgáltatásnév nem létezik a szervezetben, vagy korábban létezett, de el lett távolítva. A probléma megoldásához az erőforrás-alkalmazáshoz egy egyszerű szolgáltatásnevet kell kiépíteni a szervezeten belül, hogy az ügyfélalkalmazás engedélyeket kérjen. Az egyszerű szolgáltatás több módon is kiépíthető az alkalmazás típusától függően, beleértve a következőket:

    -   Előfizetés beszerzése az erőforrás-alkalmazáshoz (Microsoft által közzétett alkalmazások)

    -   Hozzájárulás az erőforrás-alkalmazáshoz

    -   Az alkalmazás engedélyeinek megadása a Azure Portal használatával

    -   Az alkalmazás hozzáadása az Azure AD Application Galleryből

## <a name="risky-app-error-and-warning"></a>Kockázatos alkalmazáshiba és figyelmeztetés
* **AADSTS900941:** Rendszergazdai jogosultság szükséges. Az alkalmazás kockázatnak számít. (AdminConsentRequiredDueToRiskyApp)
* Ez az alkalmazás kockázatos lehet. Ha megbízik az alkalmazásban, kérje meg a rendszergazdát, hogy engedélyezze a hozzáférést.
* **AADSTS900981:** Egy kockázatos alkalmazáshoz rendszergazdai beleegyező kérelem érkezett. (AdminConsentRequestRiskyAppWarning)
* Ez az alkalmazás kockázatos lehet. Csak akkor folytassa, ha megbízik az alkalmazásban.

Mindkét üzenet akkor jelenik meg, ha a Microsoft megállapította, hogy a beleegyezett kérelem kockázatos lehet. Számos más tényező között ez akkor fordulhat elő, ha egy [ellenőrzött közzétevőt](../develop/publisher-verification-overview.md) nem adtak hozzá az alkalmazás regisztrálásához. Az első hibakód és üzenet jelenik meg a végfelhasználók számára, ha a [rendszergazda beleegyezik a munkafolyamat](configure-admin-consent-workflow.md) le van tiltva. A második kód és üzenet jelenik meg a végfelhasználók számára, ha a rendszergazdai jogosultsági szintű munkafolyamat engedélyezve van, és a rendszergazdák. 

A végfelhasználók nem tudnak jóváhagyást adni a kockázatnak kitett alkalmazásokhoz. A rendszergazdáknak képesnek kell lenniük, de körültekintően kell értékelniük az alkalmazást, és körültekintően kell eljárniuk. Ha az alkalmazás a további felülvizsgálat során gyanúsnak tűnik, a jelentés a Microsofttól kérhető a beleegyezési képernyőn. 

## <a name="next-steps"></a>Következő lépések 

[Alkalmazások, engedélyek és beleegyezett Azure Active Directory (v1-végpont)](../develop/quickstart-register-app.md)<br>

[Hatókörök, engedélyek és beleegyezett a Azure Active Directory (v 2.0-végpont)](../develop/v2-permissions-and-consent.md)