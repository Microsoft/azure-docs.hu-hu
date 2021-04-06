---
title: Külső hozzáférés kezelése Azure Active Directory jogosultságok kezelésével
description: Azure Active Directory jogosultság-kezelés használata a teljes külső hozzáférési biztonsági csomag részeként.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f06a54f59405d9833194b2e7d4488bc93d2437ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725178"
---
# <a name="manage-external-access-with-entitlement-management"></a>A külső hozzáférés kezelése a jogosultságok kezelésével 


A [jogosultságok kezelése](../governance/entitlement-management-overview.md) egy olyan identitás-irányítási képesség, amely lehetővé teszi a szervezetek számára, hogy a hozzáférési kérelmek munkafolyamatainak automatizálásával, a hozzáférési hozzárendelésekkel, a felülvizsgálatokkal és a lejárattal együtt kezeljék az identitást A jogosultságok kezelése lehetővé teszi, hogy a delegált nem rendszergazdák olyan [hozzáférési csomagokat](../governance/entitlement-management-overview.md) hozzanak létre, amelyeket más szervezetek külső felhasználói igényelhetnek. Az egy-és többfázisú jóváhagyási munkafolyamatok konfigurálhatók a kérelmek kiértékeléséhez [, valamint a felhasználók számára](../governance/what-is-provisioning.md) az ismétlődő ellenőrzésekkel való korlátozott hozzáféréshez. A jogosultságok kezelése lehetővé teszi a házirend-alapú üzembe helyezést és a külső fiókok megszüntetését.

## <a name="key-concepts-for-enabling-entitlement-management"></a>A jogosultságok kezelésének engedélyezésével kapcsolatos főbb fogalmak

A jogosultságok kezelésének megismeréséhez a következő fontos fogalmakat kell figyelembe venni.

### <a name="access-packages"></a>Hozzáférési csomagok

A jogosultsági felügyelet alapja a [hozzáférési csomag](../governance/entitlement-management-overview.md) . A hozzáférési csomagok olyan házirend által szabályozott erőforrások csoportjai, amelyeknek a felhasználónak egy projektben kell együttműködni, vagy más feladatokat kell elvégeznie. A hozzáférési csomagok például a következők lehetnek:

* hozzáférés adott SharePoint-webhelyekhez.

* vállalati alkalmazások, például az egyéni házon belüli és SaaS-alkalmazások, például a Salesforce.

* Microsoft Teams-csatornák.

* Microsoft 365 csoportok. 

### <a name="catalogs"></a>Katalógusok

A hozzáférési csomagok a [katalógusokban](../governance/entitlement-management-catalog-create.md)találhatók. Létre kell hoznia egy katalógust, ha a kapcsolódó erőforrásokat és a hozzáférési csomagokat szeretné csoportosítani, és delegálni a felügyeletének képességét. Először vegyen fel erőforrásokat egy katalógusba, majd adja hozzá ezeket az erőforrásokat a csomagok eléréséhez. Előfordulhat például, hogy létre szeretne hozni egy "Pénzügy" katalógust, és [delegálja a felügyeletét](../governance/entitlement-management-delegate.md) a pénzügyi csapat egyik tagjának. Az adott személy ezután [erőforrásokat vehet fel](../governance/entitlement-management-catalog-create.md), hozzáférési csomagokat hozhat létre, és kezelheti a hozzáférési jóváhagyásokat ezekhez a csomagokhoz.

Az alábbi ábrán egy általános irányítási életciklus látható, amely szerint egy külső felhasználó hozzáférést szerez egy lejárati hozzáférési csomaghoz.

![A külső felhasználói irányítási ciklus diagramja.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Önkiszolgáló külső hozzáférés

Az [Azure ad saját hozzáférési portálján](../governance/entitlement-management-request-access.md) keresztül felhasználhatja az Access-csomagokat, hogy a külső felhasználók hozzáférjenek a hozzáféréshez. A szabályzatok határozzák meg, hogy kik igényelhetnek hozzáférési csomagokat. Megadhatja, hogy ki jogosult a hozzáférési csomag igénylésére:

* Adott [csatlakoztatott szervezetek](../governance/entitlement-management-organization.md)

* Minden konfigurált csatlakoztatott szervezet

* Bármely szervezet összes felhasználója

* A bérlőn már meglévő tagok vagy vendég felhasználók

### <a name="approvals"></a>Approvals   
A hozzáférési csomagok magukban foglalhatják a hozzáférés kötelező jóváhagyását. **A külső felhasználók számára mindig hajtson végre jóváhagyási folyamatokat**. A jóváhagyások lehetnek egyetlen vagy többfázisú jóváhagyások. A jóváhagyásokat szabályzatok határozzák meg. Ha a belső és külső felhasználóknak ugyanahhoz a csomaghoz is hozzá kell férniük, valószínűleg különböző hozzáférési házirendeket állítanak be a csatlakoztatott szervezetek különböző kategóriáira, illetve a belső felhasználók számára.

### <a name="expiration"></a>Lejárat  
A hozzáférési csomagok tartalmazhatnak lejárati dátumot. A lejárati idő beállítható egy adott napra, vagy a felhasználó számára megadott számú napokat adhat meg a hozzáféréshez. Ha a hozzáférési csomag lejár, és a felhasználónak nincs más hozzáférése, akkor a felhasználót jelképező B2B vendég felhasználói objektum törölhető vagy letiltható a bejelentkezésből. Javasoljuk, hogy a külső felhasználók hozzáférési csomagjainak érvényességét kényszerítse ki. Nem minden hozzáférési csomag lejár. Ha nem, akkor győződjön meg arról, hogy a hozzáférési felülvizsgálatokat hajtja végre.

### <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

A hozzáférési csomagok időszakos [hozzáférési felülvizsgálatokat](../governance/manage-guest-access-with-access-reviews.md)igényelhetnek, amelyekre szükség van a csomag tulajdonosának vagy a megbízottjának, hogy tanúsítsa a felhasználók hozzáférésének folyamatos igényét. 

A felülvizsgálat beállítása előtt határozza meg a következőket.

* ki

   * Mik a további hozzáférés feltételei?

   * Kik a megadott véleményezők?

* Milyen gyakran történjenek az ütemezett felülvizsgálatok?

   * A beépített lehetőségek közé tartozik a havi, negyedéves, bi-évenkénti vagy évenkénti. 

   * A külső hozzáférést támogató csomagok esetében negyedévente vagy gyakrabban ajánlott. 

 

> [!IMPORTANT]
> A hozzáférési csomagok hozzáférési felülvizsgálatai csak a jogosultsági felügyelet által biztosított hozzáférést vizsgálják felül. Ezért más folyamatokat kell beállítania a jogosultsági felügyeleten kívüli külső felhasználók számára biztosított hozzáférés áttekintéséhez.

A hozzáférési felülvizsgálatokkal kapcsolatos további információkért tekintse meg [Az Azure ad hozzáférési felülvizsgálatok üzembe helyezésének megtervezése](../governance/deploy-access-reviews.md)című témakört.

## <a name="using-automation-in-entitlement-management"></a>Automatizálás használata a jogosultságok kezelésében

A [jogosultsági felügyeleti funkciókat Microsoft Graph használatával](/graph/tutorial-access-package-api)is végrehajthatja, beleértve a következőket:

* [Hozzáférési csomagok kezelése](/graph/api/resources/accesspackage?view=graph-rest-beta)

* [Hozzáférési felülvizsgálatok kezelése](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta)

* [Csatlakoztatott szervezetek kezelése](/graph/api/resources/connectedorganization?view=graph-rest-beta)

* [Jogosultság-kezelési beállítások kezelése](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta)

## <a name="recommendations"></a>Javaslatok 

Javasoljuk, hogy a külső hozzáférés a jogosultság-kezeléssel való szabályozásának eljárásait javasolja.

**Egy vagy több üzleti partnerrel rendelkező projektek esetén [hozzáférési csomagokat hozhat létre és használhat](../governance/entitlement-management-access-package-create.md) a felhasználók számára az erőforrásokhoz való hozzáférés biztosításához**. 

* Ha a címtárban már van B2B-felhasználó, akkor közvetlenül is hozzárendelheti azokat a megfelelő hozzáférési csomagokhoz.

* A hozzáférést a [Azure Portal](../governance/entitlement-management-access-package-assignments.md)vagy a [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta)használatával rendelheti hozzá.

**Az identitás-irányítási beállítások használatával távolíthatja el a felhasználókat a címtárból, ha a hozzáférési csomagok lejárnak**.

![Képernyőkép a külső felhasználók életciklusának kezeléséről.](media/secure-external-access/6-manage-external-lifecycle.png)

Ezek a beállítások csak azokra a felhasználókra vonatkoznak, akik a jogosultsági felügyeleten keresztül lettek bevezetve.

A **[katalógusok és hozzáférési csomagok felügyeletének delegálása](../governance/entitlement-management-delegate.md) az üzleti tulajdonosok számára, akiknek további információval kell rendelkezniük arról, hogy kinek van hozzáférése**.

![A katalógus konfigurálásának képernyőképe.](media/secure-external-access/6-catalog-management.png)

**A külső felhasználók számára elérhető [hozzáférési csomagok elévülésének megkövetelése](../governance/entitlement-management-access-package-lifecycle-policy.md) .**


![Képernyőkép a hozzáférési csomag lejáratának konfigurálásáról.](media/secure-external-access/6-access-package-expiration.png)

* Ha ismeri a Project-alapú hozzáférési csomag befejezési dátumát, a dátum beállításával állíthatja be a megadott dátumot. 

* Ellenkező esetben javasoljuk, hogy a lejárati idő ne legyen 365 nap, hacsak nem ismert, hogy a több évre kiterjedő szerepvállalás.

* A hozzáférés kiterjesztésének engedélyezése a felhasználók számára.

* Jóváhagyás megkövetelése a bővítmény engedélyezéséhez.

**A [csomagok hozzáférési felülvizsgálatának érvénybe léptetése](../governance/manage-guest-access-with-access-reviews.md) , hogy elkerülje a vendégek nem megfelelő hozzáférését.**

![Képernyőkép új hozzáférési csomag létrehozásáról.](media/secure-external-access/6-new-access-package.png)

* A felülvizsgálatokat negyedévente kell kikényszeríteni.

* A megfelelőséggel kapcsolatos bizalmas projektek esetében a felülvizsgálók a külső felhasználókra vonatkozó önellenőrzés helyett külön véleményezők állíthatók be. Azok a felhasználók, akik hozzáférnek a Package managerekhez, jó kiindulópontot biztosítanak a felülvizsgálók számára. 

* A kevésbé érzékeny projektek esetében a felhasználók önellenőrzése csökkenti a szervezet azon terheit, amelyekkel megszűnik a felhasználók hozzáférésének megszüntetése a saját szervezete számára.

További információ: [hozzáférés szabályozása külső felhasználók számára az Azure ad-jogosultságok kezelésében](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az erőforrásokhoz való külső hozzáférés biztonságossá tételéhez. Javasoljuk, hogy a felsorolt sorrendben végezze el a műveleteket.

1. [A külső hozzáférés biztonsági helyzetének meghatározása](1-secure-access-posture.md)

2. [Aktuális állapot felderítése](2-secure-access-current-state.md)

3. [Irányítási terv létrehozása](3-secure-access-plan.md)

4. [Biztonsági csoportok használata](4-secure-access-groups.md)

5. [Áttérés az Azure AD B2B-re](5-secure-access-b2b.md)

6. [Biztonságos hozzáférés a jogosultsági felügyelettel](6-secure-access-entitlement-managment.md) (itt van.)

7. [Biztonságos hozzáférés feltételes hozzáférési szabályzatokkal](7-secure-access-conditional-access.md)

8. [Biztonságos hozzáférés érzékenységi címkékkel](8-secure-access-sensitivity-labels.md)

9. [Biztonságos hozzáférés a Microsoft Teams, a OneDrive és a SharePoint rendszerhez](9-secure-access-teams-sharepoint.md)

 

