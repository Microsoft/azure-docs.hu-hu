---
title: Az alkalmazások beleegyezett az alkalmazásokkal és a Azure Active Directoryi engedélyezési kérelmek kiértékelésével
description: Megtudhatja, hogyan kezelheti a belefoglalt kéréseket, amikor a felhasználó beleegyezik, vagy korlátozott, és hogyan értékelheti ki a bérlői szintű rendszergazdai beleegyező kéréseket Azure Active Directory-alkalmazásban.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.openlocfilehash: 189a89276d922665dd1ad0fbacc77ba499137048
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253102"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Az alkalmazások beleegyezett az alkalmazásokkal és az engedélyezési kérelmek kiértékelésével

A Microsoft [javasolja](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations) , hogy tiltsa le az alkalmazásokhoz való végfelhasználói hozzájárulásukat. Ez központosítja a szervezet biztonsági és identitás-felügyeleti csapatának döntéshozatali folyamatát.

A végfelhasználók belefoglalásának letiltását vagy korlátozását követően számos fontos szempontot kell figyelembe venni, hogy a szervezet biztonságban maradjon, miközben továbbra is lehetővé teszi az üzleti szempontból kritikus fontosságú alkalmazások használatát. Ezek a lépések elengedhetetlenek a szervezet támogatási csapatának és informatikai rendszergazdáinak, valamint a nem felügyelt fiókok harmadik féltől származó alkalmazásokban való használatának elkerüléséhez.

## <a name="process-changes-and-education"></a>Folyamatok változásai és oktatás

 1. Érdemes lehet engedélyezni a rendszergazdai [hozzájárulási munkafolyamatot (előzetes verzió)](configure-admin-consent-workflow.md) , hogy a felhasználók közvetlenül a beleegyezési képernyőről kérjenek rendszergazdai jóváhagyást.

 2. Győződjön meg arról, hogy az összes rendszergazda tisztában van az [engedélyek és a hozzájárulási keretrendszerrel](../develop/consent-framework.md), hogyan működik a [hozzájárulási](../develop/application-consent-experience.md) kérés, és hogyan [értékelhető ki a bérlői szintű rendszergazdai hozzájárulás iránti kérelem](#evaluating-a-request-for-tenant-wide-admin-consent).
 3. Tekintse át a szervezete meglévő folyamatait, hogy a felhasználók rendszergazdai jóváhagyást kérjenek egy alkalmazáshoz, és szükség esetén végezze el a szükséges frissítéseket. Ha a folyamatok módosulnak:
    * Frissítse a kapcsolódó dokumentációt, figyelést, automatizálást stb.
    * Az összes érintett felhasználó, fejlesztő, támogatási csoport és rendszergazdák számára a folyamat változásainak közlése.

## <a name="auditing-and-monitoring"></a>Naplózás és figyelés

1. Az [alkalmazások naplózása és az engedélyek](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) megadásával biztosítható, hogy ne legyenek biztosítva indokolatlan vagy gyanús alkalmazások számára az adathozzáférés.

2. Tekintse át a [tiltott engedélyezési támogatások észlelését és szervizelését az Office 365-ben](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) további ajánlott eljárások és a OAuth-hozzájárulást kérő gyanús alkalmazások elleni védelem érdekében.

3. Ha a szervezet rendelkezik a megfelelő licenccel:

    * [A Microsoft Cloud app Security további OAuth alkalmazás-naplózási funkcióit](/cloud-app-security/investigate-risky-oauth)használhatja.
    * [Azure monitor munkafüzetek használata az engedélyek és a](../reports-monitoring/howto-use-azure-monitor-workbooks.md) hozzájuk kapcsolódó tevékenységek figyeléséhez. A *beleegyező* adatellenőrzési munkafüzet az alkalmazások megtekintését teszi lehetővé a sikertelen beleegyező kérelmek száma alapján. Ez hasznos lehet a rendszergazdák számára az alkalmazások prioritásának áttekintésére és eldöntésére, hogy a rendszergazda beleegyezik-e.

### <a name="additional-considerations-for-reducing-friction"></a>További szempontok a súrlódás csökkentése érdekében

A már használatban lévő, megbízható, üzleti szempontból kritikus fontosságú alkalmazások hatásának minimalizálásához érdemes proaktív módon megadnia a rendszergazdai jóváhagyást olyan alkalmazásokhoz, amelyek nagy számú felhasználói hozzájárulást biztosítanak:

1. Készítsen leltárt a szervezethez már hozzáadott alkalmazásokról a bejelentkezési naplók vagy a jóváhagyások engedélyezése tevékenység alapján. A PowerShell- [parancsfájlok](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) segítségével gyorsan és egyszerűen felderítheti az alkalmazásokat nagy számú felhasználói hozzájárulási támogatással.

2. Értékelje ki azokat a legfelső szintű alkalmazásokat, amelyek még nem kaptak rendszergazdai beleegyezett.

   > [!IMPORTANT]
   > Körültekintően értékelje ki az alkalmazást, mielőtt a bérlői szintű rendszergazdai hozzájárulást megadta, még akkor is, ha a szervezet számos felhasználója már beleegyezett magával.

3. Minden jóváhagyott alkalmazáshoz adja meg a bérlői szintű rendszergazdai jóváhagyást az alábbi módszerek egyikének használatával.

4. Az egyes jóváhagyott alkalmazások esetében érdemes [korlátozni a felhasználói hozzáférés korlátozását](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Kérelem kiértékelése a bérlői szintű rendszergazdai engedélyhez

A bérlői szintű rendszergazdai jóváhagyás megadása kényes művelet.  Az engedélyek a teljes szervezet nevében kapnak engedélyt, és tartalmazhatnak a magas jogosultsági szintű műveletekhez szükséges engedélyeket. Például: szerepkör-kezelés, teljes hozzáférés az összes postaládához vagy az összes webhelyhez, valamint teljes körű felhasználó megszemélyesítés.

A bérlői szintű rendszergazdai jóváhagyás megadása előtt meg kell győződnie arról, hogy megbízhatónak tartja az alkalmazást és az alkalmazás közzétevőjét az Ön által biztosított hozzáférés szintjén. Ha nem biztos abban, hogy az alkalmazást milyen módon vezérli, és miért kéri az alkalmazás az engedélyeket, ne *adjon meg beleegyezést*.

Az alábbi lista néhány olyan javaslatot tartalmaz, amelyeket figyelembe kell venni a rendszergazdai jóváhagyás megadására irányuló kérelem kiértékelése során.

* **Ismerje meg az [engedélyeket és a beleegyezési keretrendszert](../develop/consent-framework.md) a Microsoft Identity platformon.**

* **Ismerje meg a [delegált engedélyek és az alkalmazás engedélyei](../develop/v2-permissions-and-consent.md#permission-types)közötti különbséget.**

   Az alkalmazás engedélyei lehetővé teszik az alkalmazás számára a teljes szervezethez tartozó adathozzáférést felhasználói beavatkozás nélkül. A delegált engedélyek lehetővé teszik, hogy az alkalmazás olyan felhasználó nevében járjon el, aki egy bizonyos ponton be lett jelentkezve az alkalmazásba.

* **Ismerje meg a kért engedélyeket.**

   Az alkalmazás által kért engedélyek szerepelnek a [hozzájárulási kérésben](../develop/application-consent-experience.md). Az engedély címének kibontásakor megjelenik az engedély leírása. Az alkalmazás engedélyeinek leírása általában "bejelentkezett felhasználó nélkül" végződik. A delegált engedélyek leírása általában a bejelentkezett felhasználó nevében végződik. A Microsoft Graph API-ra vonatkozó engedélyek leírása [Microsoft Graph engedélyek hivatkozása](/graph/permissions-reference) – további API-k dokumentációja a közzétett engedélyek megismeréséhez.

   Ha nem érti a kért engedélyt, ne adjon meg *beleegyezést*.

* **Megtudhatja, hogy melyik alkalmazás kér engedélyeket, és ki tette közzé az alkalmazást.**

   Legyen óvatos a más alkalmazásokhoz hasonló rosszindulatú alkalmazásokkal.

   Ha nem fér hozzá egy alkalmazás vagy annak közzétevője legitimitásához, ne *adjon meg jóváhagyást*. Ehelyett kérjen további megerősítést (például közvetlenül az alkalmazás közzétevője).

* **Győződjön meg arról, hogy a kért engedélyek összhangban vannak az alkalmazás által várt funkciókkal.**

   Előfordulhat például, hogy egy SharePoint-webhelyeket kezelő alkalmazás delegált hozzáférést igényel az összes webhelycsoport olvasásához, de nem feltétlenül szükséges teljes hozzáférést biztosítani az összes postaládához, vagy teljes megszemélyesítési jogosultságot a címtárban.

   Ha azt gyanítja, hogy az alkalmazás a szükségesnél több engedélyt kér, ne *adjon* meg belefoglalást. További részletekért vegye fel a kapcsolatot az alkalmazás gyártójával.

## <a name="granting-consent-as-an-administrator"></a>A jóváhagyás engedélyezése rendszergazdaként

### <a name="granting-tenant-wide-admin-consent"></a>A bérlői szintű rendszergazdai jóváhagyás megadása
Az Azure AD PowerShell-lel vagy a hozzájárulási kéréssel megadhatja, hogy a bérlői szintű rendszergazdai hozzájárulást a Azure Portal, az Azure AD PowerShell vagy a belefoglalt engedély használatával adja [meg az alkalmazásnak](grant-admin-consent.md) , amely részletes útmutatást biztosít a bérlői szintű rendszergazdai jóváhagyáshoz

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Jóváhagyás megadása egy adott felhasználó nevében
A teljes szervezethez való hozzáférés megadása helyett a rendszergazda a [Microsoft Graph API](/graph/use-the-api) -val is megadhatja a delegált engedélyeknek egy adott felhasználó nevében való jóváhagyását. További információ: [hozzáférés beszerzése egy felhasználó nevében](/graph/auth-v2-user).

## <a name="limiting-user-access-to-applications"></a>Az alkalmazásokhoz való felhasználói hozzáférés korlátozása
A felhasználók az alkalmazásokhoz való hozzáférését továbbra is korlátozhatja, még akkor is, ha a bérlői szintű rendszergazdai jogosultságot megadták. A felhasználók alkalmazáshoz való hozzárendelésének megkövetelésével kapcsolatos további információkért lásd: [a felhasználók és csoportok hozzárendelésének módszerei](./assign-user-or-group-access-portal.md).

További információk a további összetett forgatókönyvek kezeléséről: az [Azure ad használata az alkalmazás-hozzáférés kezeléséhez](what-is-access-management.md).

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Minden jövőbeli felhasználói beleegyező művelet letiltása bármely alkalmazáshoz
A teljes címtár felhasználói beleegyezésének letiltása megakadályozza, hogy a végfelhasználók bármilyen alkalmazást fogadnak el. A rendszergazdák továbbra is engedélyezhetik a felhasználó nevében való hozzájárulásukat. Ha többet szeretne megtudni az alkalmazások beleegyezéséről, és miért nem szeretne beleegyezést kapni, olvassa el a [felhasználói és rendszergazdai beleegyezést](../develop/howto-convert-app-to-be-multi-tenant.md)ismertető témakört.

Ha le szeretné tiltani az összes jövőbeli felhasználói beleegyező műveletet a teljes címtárban, kövesse az alábbi lépéseket:
1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**
2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.
3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.
4.  A navigációs menüben válassza a **felhasználók és csoportok** lehetőséget.
5.  Válassza a **Felhasználói beállítások** elemet.
6.  Tiltsa le az összes jövőbeli felhasználói megkötési műveletet úgy, hogy a **felhasználók engedélyezik, hogy az alkalmazások hozzáférjenek az adatokhoz** , és **ne** kattintson a **Save (Mentés** ) gombra.

## <a name="next-steps"></a>Következő lépések
* [Öt lépés a személyazonossági infrastruktúra biztonságossá tételéhez](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [Rendszergazdai engedélyezési munkafolyamat konfigurálása](configure-admin-consent-workflow.md)
* [A végfelhasználók alkalmazásokra vonatkozó hozzájárulásának konfigurálása](configure-user-consent.md)
* [Engedélyek és beleegyezett a Microsoft Identity platform](../develop/v2-permissions-and-consent.md)