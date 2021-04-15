---
title: Az alkalmazásokhoz való hozzájárulás kezelése és a hozzájárulási kérelmek kiértékelása a Azure Active Directory
description: Megtudhatja, hogyan kezelheti a hozzájárulási kérelmeket, ha a felhasználói jóváhagyás le van tiltva vagy korlátozva van, és hogyan értékelheti ki a bérlői rendszergazdai jóváhagyásra vonatkozó kéréseket a Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: iangithinji
ms.reviewer: phsignor
ms.openlocfilehash: 3405181f9bace023950e583dfe1a334216bf0aa0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373944"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Az alkalmazásokhoz való hozzájárulás kezelése és a hozzájárulási kérelmek értékelése

A Microsoft [javasolja](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations) az alkalmazások végfelhasználói jóváhagyásának letiltését. Ez központosítja a döntéshozatali folyamatot a szervezet biztonsági és identitás-rendszergazdai csapatával.

A végfelhasználói jóváhagyás letiltás vagy korlátozás után több fontos szempontot is figyelembe kell vennie annak érdekében, hogy a szervezet biztonságban maradjon, miközben továbbra is lehetővé teszi az üzleti fontosságú alkalmazások használatát. Ezek a lépések elengedhetetlenek a szervezet támogatási csapatára és rendszergazdáira gyakorolt hatás minimalizálásához, miközben megakadályozzák a nem használt fiókok használatát a külső alkalmazásokban.

## <a name="process-changes-and-education"></a>Változások és oktatás feldolgozása

 1. Fontolja meg a [rendszergazdai jóváhagyás munkafolyamatának engedélyezését,](configure-admin-consent-workflow.md) hogy a felhasználók közvetlenül a hozzájárulási képernyőről kérjenek rendszergazdai jóváhagyást.

 2. Győződjön meg arról, hogy az összes [](../develop/application-consent-experience.md) rendszergazda megértette az engedélyeket és a hozzájárulási keretrendszert, [](../develop/consent-framework.md)a hozzájárulási kérés működését, valamint a bérlői szintű rendszergazdai hozzájárulásra vonatkozó kérelem [kiértékelését.](#evaluating-a-request-for-tenant-wide-admin-consent)
 3. Tekintse át a szervezet meglévő felhasználói folyamatait, amelyekben rendszergazdai jóváhagyást kérhetnek egy alkalmazáshoz, és szükség esetén frissítést is kérhetnek. Ha a folyamatok módosulnak:
    * Frissítse a vonatkozó dokumentációt, monitorozást, automatizálást stb.
    * A folyamat változásainak kommunikálása az összes érintett felhasználóval, fejlesztővel, támogatási csapattal és informatikai rendszergazdával.

## <a name="auditing-and-monitoring"></a>Naplózás és figyelés

1. [Naplózhatja az alkalmazásokat,](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) és engedélyeket kapott a szervezetben annak érdekében, hogy korábban ne kapjon hozzáférést az adatokhoz a nem biztonságos vagy gyanús alkalmazások számára.

2. Az [Office 365-ben](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) a hamis hozzájárulási jogosultságok észlelése és szervizelése cikk további ajánlott eljárásokat és védelmet biztosít az OAuth-hozzájárulást kérő gyanús alkalmazások ellen.

3. Ha a szervezet rendelkezik a megfelelő licenccel:

    * További [OAuth alkalmazás-naplózási funkciókat a](/cloud-app-security/investigate-risky-oauth)Microsoft Cloud App Security.
    * A [Azure Monitor munkafüzetek használatával figyelheti az engedélyeket és a hozzájárulással](../reports-monitoring/howto-use-azure-monitor-workbooks.md) kapcsolatos tevékenységeket. A *Consent Insights* munkafüzetben megtekintheti az alkalmazásokat a sikertelen hozzájárulási kérelmek száma alapján. Ez hasznos lehet az alkalmazások rendszergazdák számára való rangsorolatában, hogy áttekintsék és eldöntsék, meg kell-e adni nekik a rendszergazdai jóváhagyást.

### <a name="additional-considerations-for-reducing-friction"></a>További szempontok a súrlódások csökkentéséhez

A már használatban van megbízható, üzletileg kritikus alkalmazásokra gyakorolt hatás minimalizálása érdekében fontolja meg a rendszergazdai jóváhagyás proaktív megadását a nagy számú felhasználói jóváhagyással rendelkező alkalmazásokhoz:

1. A bejelentkezési naplók vagy a hozzájárulási engedélyekkel kapcsolatos tevékenységek alapján leltárt kell venni a szervezetben már hozzáadott alkalmazásokról. A [PowerShell-szkriptek](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) segítségével gyorsan és egyszerűen felderíthetők a nagy számú felhasználói hozzájárulással megadott alkalmazások.

2. Értékelje ki a legfontosabb alkalmazásokat, amelyek még nem kaptak rendszergazdai jóváhagyást.

   > [!IMPORTANT]
   > Alaposan értékelje ki az alkalmazást, mielőtt bérlői szintű rendszergazdai hozzájárulást ad, még akkor is, ha a szervezet számos felhasználója már bele is egy adott magának.

3. Minden jóváhagyott alkalmazáshoz adjon bérlői szintű rendszergazdai jóváhagyást az alább dokumentált módszerek egyikével.

4. Minden jóváhagyott alkalmazáshoz érdemes korlátozni [a felhasználói hozzáférést.](configure-user-consent.md)

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Bérlői szintű rendszergazdai hozzájárulásra vonatkozó kérelem értékelése

A bérlői szintű rendszergazdai jóváhagyás megadása bizalmas művelet.  Az engedélyek a teljes szervezet nevében meg lesznek adni, és tartalmazhatnak a kiemelt jogosultságú műveletek megkísérléséhez szükséges engedélyeket is. Ilyen például a szerepkörkezelés, az összes postaláda vagy az összes hely teljes hozzáférése és a teljes felhasználói megszemélyesítés.

A bérlői szintű rendszergazdai jóváhagyás megadása előtt meg kell győződnie arról, hogy az alkalmazás és az alkalmazás közzétevője megbízik az ön által biztosított hozzáférési szinten. Ha nem biztos abban, hogy tudja, ki vezérli az alkalmazást, és miért kéri az engedélyeket, ne *adja meg a hozzájárulást.*

Az alábbi lista néhány javaslatot tartalmaz, amelyek a rendszergazdai hozzájárulás megadására vonatkozó kérelem kiértékelését ajánlásokat tartalmaznak.

* **A Microsoft [identitásplatformján az engedélyeket és a](../develop/consent-framework.md) hozzájárulási keretrendszert értheti meg.**

* **A delegált és az alkalmazásengedélyek [közötti különbség.](../develop/v2-permissions-and-consent.md#permission-types)**

   Az alkalmazásengedélyek lehetővé teszik, hogy az alkalmazás a teljes szervezet adataihoz hozzáférjen felhasználói beavatkozás nélkül. A delegált engedélyek lehetővé teszik, hogy az alkalmazás egy olyan felhasználó nevében jár el, aki egy adott ponton bejelentkezett az alkalmazásba.

* **A kért engedélyeket értse meg.**

   Az alkalmazás által kért engedélyek a hozzájárulási [kérésben vannak felsorolva.](../develop/application-consent-experience.md) Az engedély címének kibontása megjeleníti az engedély leírását. Az alkalmazásengedélyek leírása általában "bejelentkezett felhasználó nélkül" végződik. A delegált engedélyek leírása általában a következőre végződik: "a bejelentkezett felhasználó nevében". A Microsoft Graph API engedélyeinek leírását az [Microsoft Graph Permissions Reference](/graph/permissions-reference) (Engedélyek referenciája) dokumentumban találhatja meg. A további API-k dokumentációjában tájékozódhat az elérhetővé tévő engedélyekről.

   Ha nem érti a kért engedélyeket, *ne adja meg a hozzájárulást.*

* **Annak a tudomásán, hogy melyik alkalmazás kér engedélyeket, és ki tette közzé az alkalmazást.**

   Legyen óvatos az olyan rosszindulatú alkalmazásokkal, amelyek más alkalmazásokhoz hasonlónak próbálnak kinézni.

   Ha nem biztos egy alkalmazás vagy annak közzétevője hitelességében, *ne adja meg a hozzájárulást.* Ehelyett kérjen további megerősítést (például közvetlenül az alkalmazás-közzétevőtől).

* **Győződjön meg arról, hogy a kért engedélyek igazodnak az alkalmazástól elvárt funkciókhoz.**

   Előfordulhat például, hogy egy SharePoint-webhelykezelést kínáló alkalmazásnak delegált hozzáférésre van szüksége az összes webhelycsoport olvasásához, de nem feltétlenül van szükség teljes hozzáférésre az összes postaládához vagy a címtár teljes megszemélyesítési jogosultságára.

   Ha azt gyanítja, hogy az alkalmazás a szükségesnél több engedélyt kér, *ne adja meg a hozzájárulást.* További részletekért lépjen kapcsolatba az alkalmazás kiadójával.

## <a name="granting-consent-as-an-administrator"></a>Hozzájárulás megadása rendszergazdaként

### <a name="granting-tenant-wide-admin-consent"></a>Bérlői szintű rendszergazdai jóváhagyás megadása
Lásd: [Grant tenant-wide admin consent to an application](grant-admin-consent.md) (Bérlői szintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz) című témakörben részletes útmutatást kap a bérlői szintű rendszergazdai jóváhagyás Azure Portal-ból, az Azure AD PowerShell használatával vagy a hozzájárulási kérésből.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Hozzájárulás megadása egy adott felhasználó nevében
Ahelyett, hogy a teljes szervezet számára jóváhagyást ad, a rendszergazda a [Microsoft Graph API-val](/graph/use-the-api) is engedélyt adhat a delegált engedélyekhez egyetlen felhasználó nevében. További információ: [Hozzáférés lekérte egy felhasználó nevében.](/graph/auth-v2-user)

## <a name="limiting-user-access-to-applications"></a>A felhasználók alkalmazásokhoz való hozzáférésének korlátozása
A felhasználók alkalmazásokhoz való hozzáférése továbbra is korlátozható, még akkor is, ha a bérlői szintű rendszergazdai hozzájárulást megadták. A felhasználók alkalmazáshoz való hozzárendelésének megkövetelésével kapcsolatos további információkért lásd a felhasználók és csoportok [hozzárendelésének módszereit.](./assign-user-or-group-access-portal.md)

A további összetett forgatókönyvek kezelésével kapcsolatos általánosabb információkért lásd: Using Azure AD for application access management (Az Azure AD használata [alkalmazás-hozzáférés-kezeléshez).](what-is-access-management.md)

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Az összes jövőbeli felhasználói hozzájárulási művelet letiltása bármely alkalmazáshoz
A felhasználói jóváhagyás letiltása a teljes címtárra vonatkozik, így a végfelhasználók nem járulnak hozzá az alkalmazásokhoz. A rendszergazdák továbbra is járulnak hozzá a felhasználó nevében. Az alkalmazás-jóváhagyással kapcsolatos további információkért, valamint annak okával kapcsolatban, hogy Ön miért szeretne vagy miért nem, olvassa el A felhasználói és rendszergazdai [hozzájárulás ismertetése című témakört.](../develop/howto-convert-app-to-be-multi-tenant.md)

Ha le szeretné tiltani az összes jövőbeli felhasználói hozzájárulási műveletet a teljes címtárban, kövesse az alábbi lépéseket:
1.  Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**
2.  Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.
3.  Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.
4.  A **navigációs menüben válassza a** Felhasználók és csoportok lehetőséget.
5.  Válassza a **Felhasználói beállítások** elemet.
6.  Tiltsa le az összes  jövőbeli felhasználói hozzájárulási műveletet. Ehhez a Felhasználók engedélyezhetik az alkalmazásoknak az adataikhoz való hozzáférést beállítást a Nem beállításra, majd **kattintson** a **Mentés gombra.**

## <a name="next-steps"></a>Következő lépések
* [Öt lépés az identitás-infrastruktúra biztonságossá tétele érdekében](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [A rendszergazdai jóváhagyás munkafolyamatának konfigurálása](configure-admin-consent-workflow.md)
* [A végfelhasználók alkalmazásokra vonatkozó hozzájárulásának konfigurálása](configure-user-consent.md)
* [Engedélyek és jóváhagyás a Microsoft identitásplatformján](../develop/v2-permissions-and-consent.md)
