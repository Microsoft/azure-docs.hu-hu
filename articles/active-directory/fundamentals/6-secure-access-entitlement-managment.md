---
title: Külső hozzáférés kezelése a Azure Active Directory jogosultságkezelésével
description: A Azure Active Directory jogosultságkezelés használata a teljes külső hozzáférés biztonsági tervének részeként.
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
ms.openlocfilehash: 89744b63a555cc02d35815b4066ce572b7f77e38
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531896"
---
# <a name="manage-external-access-with-entitlement-management"></a>Külső hozzáférés kezelése jogosultságkezeléssel 


[A jogosultságkezelés](../governance/entitlement-management-overview.md) egy olyan identitáskezelési képesség, amely lehetővé teszi a szervezetek számára az identitás- és hozzáférési életciklus nagy léptékű kezelését a hozzáférési kérelmek munkafolyamatának, hozzáférés-hozzárendeléseinek, felülvizsgálatának és lejáratának automatizálásával. A jogosultságkezelés lehetővé teszi, hogy a meghatalmazott nem rendszergazda felhasználók hozzáférési csomagokat hozzanak [létre,](../governance/entitlement-management-overview.md) amelyekhez más szervezetek külső felhasználói hozzáférést kérhetnek. Egy és többszakaszos jóváhagyási munkafolyamatok konfigurálhatóak a [](../governance/what-is-provisioning.md) kérések kiértékeléséhez, valamint a felhasználók időkorrekulált hozzáféréshez való, ismétlődő felülvizsgálatokkal való ellátásához. A jogosultságkezelés lehetővé teszi a külső fiókok szabályzatalapú kiépítését és megszüntetését.

## <a name="key-concepts-for-enabling-entitlement-management"></a>A jogosultságkezelés engedélyezésének fő fogalmai

A jogosultságok kezelésével kapcsolatos fontos fogalmak a következők.

### <a name="access-packages"></a>Hozzáférési csomagok

A [hozzáférési csomag](../governance/entitlement-management-overview.md) a jogosultságkezelés alapja. A hozzáférési csomagok olyan szabályzat által szabályozott erőforrások csoportjai, amelyekre a felhasználónak a projekten való együttműködéshez vagy más feladatok elvégzéséhez van szüksége. Egy hozzáférési csomag például a következőket tartalmazhatja:

* hozzáférés adott SharePoint-webhelyekhez.

* vállalati alkalmazások, beleértve az egyéni, házon belül készült és SaaS-alkalmazásokat, például a Salesforce-t.

* Microsoft Teams-csatornák.

* Microsoft 365-csoportok. 

### <a name="catalogs"></a>Katalógusok

A hozzáférési csomagok a [katalógusban találhatók.](../governance/entitlement-management-catalog-create.md) Katalógust akkor hoz létre, ha csoportosítja a kapcsolódó erőforrásokat és szeretné elérni a csomagokat, és delegálni szeretné a kezelésük képességét. Először erőforrásokat ad hozzá egy katalógushoz, majd hozzáadhatja ezeket az erőforrásokat a csomagok eléréséhez. Előfordulhat például, hogy létre szeretne hozni egy [](../governance/entitlement-management-delegate.md) "Pénzügyi" katalógust, és delegálni szeretné a felügyeletét a pénzügyi csapat egyik tagnak. Ez a személy ezután [erőforrásokat adhat hozzá,](../governance/entitlement-management-catalog-create.md)hozzáférési csomagokat hozhat létre, és kezelheti a csomagok hozzáférési jóváhagyását.

Az alábbi ábra egy tipikus cégirányítási életciklust mutat be, amikor egy külső felhasználó hozzáfér egy lejárati idővel rendelkező hozzáférési csomaghoz.

![A külső felhasználóirányítási ciklus diagramja.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Önkiszolgáló külső hozzáférés

A hozzáférési csomagokat az [Azure AD Saját hozzáférés portálon](../governance/entitlement-management-request-access.md) keresztül teheti elérhetőre, így külső felhasználók is kérhetnek hozzáférést. A szabályzatok határozzák meg, hogy ki kérhet hozzáférési csomagot. Megadhatja, hogy ki kérheti a hozzáférési csomagot:

* Adott [csatlakoztatott szervezetek](../governance/entitlement-management-organization.md)

* Az összes konfigurált csatlakoztatott szervezet

* Bármely szervezet összes felhasználója

* A bérlőben már tag- vagy vendégfelhasználók

### <a name="approvals"></a>Approvals   
A hozzáférési csomagok tartalmazhatják a hozzáférés kötelező jóváhagyását. **Mindig alkalmaznak jóváhagyási folyamatokat a külső felhasználók számára.** A jóváhagyások egy vagy több szakaszból álló jóváhagyások is lehetek. A jóváhagyásokat szabályzatok határozzák meg. Ha a belső és a külső felhasználóknak is ugyanannak a csomagnak kell hozzáférniük, valószínűleg különböző hozzáférési szabályzatokat fog beállítani a csatlakoztatott szervezetek különböző kategóriáihoz és a belső felhasználókhoz.

### <a name="expiration"></a>Lejárat  
A hozzáférési csomagok lejárati dátumot is tartalmazhatnak. A lejárat beállítható egy adott napra, vagy adhat a felhasználónak megadott számú napot a hozzáféréshez. Ha a hozzáférési csomag lejár, és a felhasználónak nincs más hozzáférése, a felhasználót képviselő B2B vendégfelhasználói objektum törölhető vagy letiltható a bejelentkezésben. Javasoljuk, hogy a hozzáférési csomagok lejáratát kényszerítsen ki külső felhasználók számára. Nem minden hozzáférési csomag rendelkezik lejárati idővel. Azok számára, akik nem, győződjön meg arról, hogy hozzáférési felülvizsgálatokat végez.

### <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

A hozzáférési csomagok rendszeres hozzáférési [felülvizsgálatokat](../governance/manage-guest-access-with-access-reviews.md)követelnek meg, amelyekhez a csomag tulajdonosának vagy egy tervezőnek igazolnia kell, hogy továbbra is szükség van a felhasználók hozzáférésére. 

A felülvizsgálat beállítása előtt határozza meg a következőket.

* ki

   * Milyen feltételek teljesülnek a folyamatos hozzáféréshez?

   * Kik a megadott felülvizsgálók?

* Milyen gyakran kell ütemezett felülvizsgálatokat ütemezni?

   * A beépített lehetőségek közé tartozik a havi, negyedéves, bi-éves vagy éves. 

   * A külső hozzáférést támogató csomagok esetében negyedévente vagy gyakrabban ajánlott. 

 

> [!IMPORTANT]
> A hozzáférési csomagok hozzáférési felülvizsgálata csak a jogosultságkezelésen keresztül biztosított hozzáférést vizsgálja meg. Ezért más folyamatokat is be kell állítania a jogosultságkezelésen kívüli külső felhasználók számára biztosított hozzáférések áttekintéséhez.

További információ a hozzáférési felülvizsgálatokkal kapcsolatban: [Planning an Azure AD Access Reviews deployment (Az Azure AD hozzáférési felülvizsgálatok üzembe helyezésének megtervezése).](../governance/deploy-access-reviews.md)

## <a name="using-automation-in-entitlement-management"></a>Automatizálás használata a jogosultságkezelésben

A jogosultságkezelési funkciókat a [következő Microsoft Graph](/graph/tutorial-access-package-api)végezheti el:

* [Hozzáférési csomagok kezelése](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true)

* [Hozzáférési felülvizsgálatok kezelése](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)

* [Csatlakoztatott szervezetek kezelése](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true)

* [Jogosultságkezelési beállítások kezelése](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta&preserve-view=true)

## <a name="recommendations"></a>Javaslatok 

Javasoljuk a külső hozzáférés jogosultságkezeléssel történő szabályozásának gyakorlatát.

**Ha egy vagy több üzleti [](../governance/entitlement-management-access-package-create.md) partnerrel rendelkezik,** hozzon létre és használjon hozzáférési csomagokat a partnerfelhasználók erőforrásokhoz való hozzáférésének létrehozásához és létrehozásához. 

* Ha már rendelkezik B2B-felhasználókkal a címtárban, közvetlenül is hozzárendelheti őket a megfelelő hozzáférési csomagokhoz.

* A hozzáférést a következő Azure Portal rendelheti hozzá: [,](../governance/entitlement-management-access-package-assignments.md)vagy [Microsoft Graph.](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta&preserve-view=true)

Az Identity Governance beállításaival eltávolíthat felhasználókat a **címtárból, amikor a hozzáférési csomagjaik lejárnak.**

![Képernyőkép a külső felhasználók életciklusának konfigurálásról.](media/secure-external-access/6-manage-external-lifecycle.png)

Ezek a beállítások csak a jogosultságkezelésen keresztül regisztrált felhasználókra vonatkoznak.

**[Delegálhatja a katalógusok és](../governance/entitlement-management-delegate.md)** hozzáférési csomagok kezelését az üzleti tulajdonosoknak, akik több információt tartalmaznak arról, hogy kinek kell hozzáférniük a szolgáltatáshoz.

![Képernyőkép a katalógus konfigurálásról.](media/secure-external-access/6-catalog-management.png)

**[Kikényszeríteni a hozzáférési csomagok lejáratát,](../governance/entitlement-management-access-package-lifecycle-policy.md) amelyekhez külső felhasználók is hozzáférhetnek.**


![Képernyőkép a hozzáférési csomag lejáratának konfigurálásról.](media/secure-external-access/6-access-package-expiration.png)

* Ha ismeri egy projektalapú hozzáférési csomag záró dátumát, a Dátum beállításnál adja meg az adott dátumot. 

* Ellenkező esetben javasoljuk, hogy a lejárati idő ne legyen 365 nap, kivéve, ha ismert, hogy több éves kötelezettségvállalásról van szó.

* A hozzáférés kiterjesztésének engedélyezése a felhasználók számára.

* Jóváhagyás szükséges a bővítmény megadásához.

**[Kényszerítheti a csomagok hozzáférési felülvizsgálatának kényszerítését](../governance/manage-guest-access-with-access-reviews.md) a vendégek nem megfelelő hozzáférésének elkerülése érdekében.**

![Képernyőkép egy új hozzáférési csomag létrehozásáról.](media/secure-external-access/6-new-access-package.png)

* Felülvizsgálatok kikényszerezése negyedévente.

* A megfelelőségi szempontból érzékeny projektek esetében a felülvizsgálókat állítsa adott felülvizsgálókra, és ne külső felhasználókra vonatkozó önértékelést. A hozzáférésicsomag-kezelő felhasználók jó kezdők a felülvizsgálók számára. 

* A kevésbé bizalmas projektek esetében a felhasználók önértékelése csökkenti a szervezetre háruló terheket, hogy megszűküljön a hozzáférés a már nem a saját szervezettel rendelkező felhasználóktól.

További információ: Külső felhasználók [hozzáférésének szabályozása az Azure AD-jogosultságkezelésben](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az erőforrásokhoz való külső hozzáférés biztonságossá tétele érdekében. Javasoljuk, hogy a műveleteket a listában szereplő sorrendben vegye fel.

1. [A külső hozzáférés biztonsági útjára vonatkozó biztonsági környezet meghatározása](1-secure-access-posture.md)

2. [Az aktuális állapot felderítése](2-secure-access-current-state.md)

3. [Irányítási terv létrehozása](3-secure-access-plan.md)

4. [Csoportok használata a biztonság érdekében](4-secure-access-groups.md)

5. [Áttérés az Azure AD B2B-re](5-secure-access-b2b.md)

6. [Biztonságos hozzáférés jogosultságkezeléssel](6-secure-access-entitlement-managment.md) (itt van).)

7. [Biztonságos hozzáférés feltételes hozzáférési szabályzatokkal](7-secure-access-conditional-access.md)

8. [Biztonságos hozzáférés bizalmasság címkékkel](8-secure-access-sensitivity-labels.md)

9. [Biztonságos hozzáférés a Microsoft Teamshez, a OneDrive-hoz és a SharePointhoz](9-secure-access-teams-sharepoint.md)

 

