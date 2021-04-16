---
title: Váratlan hiba az alkalmazáshoz való hozzájárulás végrehajtásakor | Microsoft Docs
description: Ismerteti azokat a hibákat, amelyek az alkalmazáshoz való hozzájárulás folyamata során léphetnek fel, és ismerteti, hogy mit tehetünk velük kapcsolatban
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad216d0062928fc16b0f2226daabb6258d09063c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378125"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Váratlan hibaüzenet jelenik meg, amikor hozzájárulást adok egy alkalmazáshoz

Ez a cikk az alkalmazáshoz való hozzájárulás folyamata során előforduló hibákat ismerteti. Ha olyan váratlan hozzájárulási kéréseket hárít el, amelyek nem tartalmaznak hibaüzeneteket, tekintse meg az [Azure AD](../develop/authentication-vs-authorization.md)hitelesítési forgatókönyveit.

Számos, a Azure Active Directory integrálható alkalmazásnak engedélyre van szüksége más erőforrások eléréséhez a működéshez. Ha ezek az erőforrások integrálva vannak a Azure Active Directory, az ezekhez való hozzáférésre vonatkozó engedélyeket gyakran a közös hozzájárulási keretrendszer használatával kérik le. Megjelenik egy hozzájárulási kérés, amely általában az alkalmazás első használatakor fordul elő, de az alkalmazás későbbi használata során is előfordulhat.

Bizonyos feltételeknek teljesülnie kell ahhoz, hogy a felhasználó elfogadja az alkalmazás által megkövetelt engedélyeket. Ha ezek a feltételek nem teljesülnek, a következő hibák léphetnek fel.

## <a name="requesting-not-authorized-permissions-error"></a>Nem engedélyezett engedélyek kérése hiba
* **AADSTS90093:** &lt; A clientAppDisplayName egy vagy több olyan engedélyt kér, amelyek megadására Ön &gt; nem jogosult. Lépjen kapcsolatba egy rendszergazdával, aki az Ön nevében beleegyezhet az alkalmazásba.
* **AADSTS90094:** &lt; A clientAppDisplayName tulajdonságnak engedélyre van szüksége ahhoz, hogy hozzáférjen a szervezetben csak egy rendszergazda által &gt; megadó erőforrásokhoz. Kérjen engedélyt a rendszergazdától az alkalmazáshoz, hogy használhassa azt.

Ez a hiba akkor fordul elő, ha egy nem globális rendszergazda felhasználó olyan alkalmazást próbál használni, amely csak egy rendszergazda által megadó engedélyeket kér. Ezt a hibát úgy oldhatja meg, ha egy rendszergazda hozzáférést ad az alkalmazáshoz a szervezet nevében.

Ez a hiba akkor is előfordulhat, ha egy felhasználó nem tud jóváhagyást adni egy alkalmazásnak, mert a Microsoft kockázatosnak érzékeli az engedélykérést. Ebben az esetben a rendszer naplózza a naplózási eseményeket az "ApplicationManagement" kategóriával, a "Hozzájárulás az alkalmazáshoz" tevékenységtípussal és a "Kockázatos alkalmazás észlelve" állapot okával.

Egy másik forgatókönyv, amelyben ez a hiba akkor fordulhat elő, ha a felhasználó-hozzárendelésre van szükség az alkalmazáshoz, de nem kapott rendszergazdai jóváhagyást. Ebben az esetben a rendszergazdának meg kell adnia a rendszergazdai hozzájárulást.   

## <a name="policy-prevents-granting-permissions-error"></a>A szabályzat megakadályozza az engedélyek megadását hiba
* **AADSTS90093:** A tenantDisplayName rendszergazdája beállított egy szabályzatot, amely megakadályozza, hogy megadja az alkalmazás nevének a kért &lt; &gt; &lt; &gt; engedélyeket. Lépjen kapcsolatba a tenantDisplayName rendszergazdájával, aki engedélyeket adhat az alkalmazásnak &lt; &gt; az Ön nevében.

Ez a hiba akkor fordul elő, ha egy globális rendszergazda kikapcsolja a felhasználók számára az alkalmazásokhoz való hozzájárulás képességét, majd egy nem rendszergazdai felhasználó megpróbál egy jóváhagyást igénylő alkalmazást használni. Ezt a hibát úgy oldhatja meg, ha egy rendszergazda hozzáférést ad az alkalmazáshoz a szervezet nevében.

## <a name="intermittent-problem-error"></a>Időszakos problémahiba
* **AADSTS90090:** Úgy tűnik, hogy a bejelentkezési folyamat időszakos problémába ütközött a clientAppDisplayName számára megkísérelt engedélyek rögzítése &lt; &gt; során. Próbálkozzon újra később.

Ez a hiba azt jelzi, hogy időszakos szolgáltatásoldali probléma történt. Ez úgy oldható meg, ha megpróbálja ismét jóváhagyást adni az alkalmazásnak.

## <a name="resource-not-available-error"></a>Az erőforrás nem érhető el hiba
* **AADSTS65005:** A clientAppDisplayName alkalmazás engedélyeket kért egy nem elérhető &lt; &gt; &lt; resourceAppDisplayName erőforrás &gt; eléréséhez. 

Lépjen kapcsolatba az alkalmazás fejlesztőjével.

##  <a name="resource-not-available-in-tenant-error"></a>Az erőforrás nem érhető el a bérlőben hiba
* **AADSTS65005:** &lt; A clientAppDisplayName egy olyan erőforráshoz kér hozzáféréstAppDisplayName erőforráshoz, amely nem érhető el &gt; a &lt; &gt; &lt; szervezetben tenantDisplayName &gt; . 

Győződjön meg arról, hogy ez az erőforrás elérhető, vagy lépjen kapcsolatba a &lt; tenantDisplayName &gt; rendszergazdájával.

## <a name="permissions-mismatch-error"></a>Engedélyel nem egyező hiba
* **AADSTS65005:** Az alkalmazás jóváhagyást kért a &lt; resourceAppDisplayName erőforrás &gt; eléréséhez. Ez a kérés nem sikerült, mert nem egyezik meg az alkalmazás alkalmazásregisztráció során való előre konfigurált beállításaival. Lépjen kapcsolatba az alkalmazás gyártójával.**

Ezek a hibák mind akkor fordulnak elő, ha az alkalmazás, amelyhez a felhasználó jóváhagyást próbál adni, engedélyeket kér egy olyan erőforrás-alkalmazáshoz való hozzáféréshez, amely nem található a szervezet címtárában (bérlőben). Ez a helyzet több okból is előfordulhat:

-   Az ügyfélalkalmazás fejlesztője helytelenül konfigurálta az alkalmazást, ezért érvénytelen erőforráshoz való hozzáférést kér. Ebben az esetben az alkalmazás fejlesztőjének frissítenie kell az ügyfélalkalmazás konfigurációját a probléma megoldásához.

-   A célerőforrás-alkalmazást képviselő szolgáltatásnév nem létezik a szervezetben, vagy korábban már létezett, de el lett távolítva. A probléma megoldásához ki kellépítenünk egy szolgáltatásnévvel az erőforrás-alkalmazáshoz a szervezetben, hogy az ügyfélalkalmazás engedélyeket kérjen hozzá. A szolgáltatásnév az alkalmazás típusától függően többféleképpen is kiépítható, beleértve a következőket:

    -   Előfizetés megszerzése az erőforrás-alkalmazáshoz (a Microsoft által közzétett alkalmazások)

    -   Hozzájárulás az erőforrás-alkalmazáshoz

    -   Engedélyek megadása az alkalmazás számára a Azure Portal

    -   Az alkalmazás hozzáadása az Azure AD-alkalmazásgyűjteményből

## <a name="risky-app-error-and-warning"></a>Kockázatos alkalmazáshiba és figyelmeztetés
* **AADSTS900941:** Rendszergazdai jóváhagyás szükséges. Az alkalmazás kockázatosnak számít. (AdminConsentRequiredDueToRiskyApp)
* Ez az alkalmazás kockázatos lehet. Ha megbízik ebben az alkalmazásban, kérje meg a rendszergazdát, hogy adjon hozzáférést.
* **AADSTS900981:** Rendszergazdai hozzájárulási kérelem érkezett egy kockázatos alkalmazáshoz. (AdminConsentRequestRiskyAppWarning)
* Ez az alkalmazás kockázatos lehet. Csak akkor folytassa, ha megbízik ebben az alkalmazásban.

Mindkét üzenet megjelenik, ha a Microsoft megállapította, hogy a hozzájárulási kérelem kockázatos lehet. Ez számos más tényező mellett akkor fordulhat elő, ha [egy](../develop/publisher-verification-overview.md) ellenőrzött közzétevő nem lett hozzáadva az alkalmazásregisztrációhoz. Az első hibakód és üzenet akkor jelenik meg a végfelhasználók számára, ha a rendszergazdai [jóváhagyás munkafolyamata](configure-admin-consent-workflow.md) le van tiltva. A második kód és üzenet akkor jelenik meg a végfelhasználók és a rendszergazdák számára, ha a rendszergazdai hozzájárulási munkafolyamat engedélyezve van. 

A végfelhasználók nem adhatnak beleegyezést a kockázatosként észlelt alkalmazásokhoz. A rendszergazdák képesek, de nagyon körültekintően kell értékelniük az alkalmazást, és körültekintően kell eljárniuk. Ha az alkalmazás gyanúsnak tűnik a további felülvizsgálat után, a jóváhagyás képernyőjén jelentheti a Microsoftnak. 

## <a name="next-steps"></a>Következő lépések 

[Alkalmazások, engedélyek és hozzájárulás Azure Active Directory (v1 végpont)](../develop/quickstart-register-app.md)<br>

[Hatókörök, engedélyek és hozzájárulás a Azure Active Directory (2.0-s végpont)](../develop/v2-permissions-and-consent.md)