---
title: Biztonsági terv létrehozása a Azure Active Directoryhoz való külső hozzáféréshez
description: Tervezze meg a szervezet erőforrásaihoz való külső hozzáférés biztonságát.
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
ms.openlocfilehash: 266142240ba9e892c905ac8aa6521da5a14c4c3d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554020"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. biztonsági terv létrehozása külső hozzáféréshez 

Most, hogy [meghatározta a külső hozzáféréshez szükséges](1-secure-access-posture.md) biztonsági testhelyzetet, és [felderíti a jelenlegi együttműködési állapotát](2-secure-access-current-state.md), létrehozhat egy külső felhasználói biztonsági és irányítási tervet. 

A tervnek a következőket kell dokumentálnia:

* Az alkalmazásoknak és egyéb erőforrásoknak, amelyeket el kell csoportosítani a hozzáféréshez.

* A külső felhasználók megfelelő bejelentkezési feltételei. Ilyen lehet például az eszköz állapota, a bejelentkezési hely, az ügyfélalkalmazás követelményei és a felhasználói kockázat.

* Üzleti szabályzatok a hozzáférés áttekintéséhez és eltávolításához. 

* A felhasználói populációkat hasonlóképpen kell csoportosítani és kezelni.

A területek dokumentálása után az identitás-és hozzáférés-kezelési szabályzatokat a Microsoft vagy bármely más identitás-szolgáltató (identitásszolgáltató) segítségével megvalósíthatja a terv megvalósításához.

## <a name="document-resources-to-be-grouped-for-access"></a>A hozzáféréshez csoportosítani kívánt erőforrások dokumentálása

Az erőforrások több módon is csoportosíthatók a hozzáféréshez. 

* A Microsoft Teams egy helyen csoportosítja a fájlokat, a beszélgetési szálakat és az egyéb erőforrásokat. A Microsoft Teams szolgáltatáshoz külső hozzáférési stratégiát kell kialakítani. Lásd: [biztonságos hozzáférés a csapatokhoz, a OneDrive és a sharepointhoz](9-secure-access-teams-sharepoint.md).

* A jogosultságok felügyeleti hozzáférési csomagjai lehetővé teszik az alkalmazások és egyéb erőforrások egyetlen csomagjának létrehozását, amelyhez hozzáférést biztosíthat. 

* A feltételes hozzáférési szabályzatok akár 250 alkalmazásra is alkalmazhatók ugyanazzal a hozzáférési követelményekkel.

A hozzáférés kezeléséhez azonban dokumentálni kell, hogy mely alkalmazásokat kell csoportosítani. A szempontoknak a következőket kell tartalmazniuk:

* **Kockázati profil**. Mi a kockázata a vállalatnak, ha egy rossz színész beszerzett egy alkalmazást? Érdemes lehet az alkalmazások magas, közepes vagy alacsony kockázatú kódolását. Legyen körültekintő, ha alacsony kockázatú, magas kockázatú alkalmazásokat kell csoportosítani. 

   * Olyan alkalmazások dokumentálása, amelyek soha nem oszthatók meg külső felhasználókkal is.

* **Megfelelőségi keretrendszerek**. Mi a teendő, ha az alkalmazásnak meg kell felelnie a megfelelőségi keretrendszerek? Mik a hozzáférési és felülvizsgálati követelmények?

* **Adott munkaköri szerepkörökhöz vagy részlegekhez tartozó alkalmazások**. Vannak olyan alkalmazások, amelyeknek csoportosítva kell lenniük, mert egy adott feladathoz vagy részleghez tartozó összes felhasználónak hozzáférésre van szüksége?

* **Együttműködésre fókuszált alkalmazások**. Milyen együttműködésre fókuszált alkalmazások férhetnek hozzá a külső felhasználókhoz? Előfordulhat, hogy a Microsoft Teams és a SharePoint elérhetőnek kell lennie. Az Office 365-as, például a Word és az Excel alkalmazáson belüli hatékonyságnövelő alkalmazások esetén a külső felhasználók saját licenceket fognak igénybe venni, vagy licenccel kell rendelkezniük, és meg kell adniuk a hozzáférést?

A külső felhasználók számára elérhetővé tenni kívánt alkalmazások és erőforrások minden egyes csoportja számára a következőket dokumentálja:

* A csoport leíró neve, például *High_Risk_External_Access_Finance*. 

* A csoportban lévő összes alkalmazás és erőforrás teljes listája.

* Az alkalmazás-és erőforrás-tulajdonosok és kapcsolattartási adatok.

* Azt határozza meg, hogy a hozzáférést az informatikai részleg szabályozza-e, vagy delegált a vállalat tulajdonosának.

* Az előfeltételeket, például a háttér-ellenőrzés vagy a betanítás betöltését a hozzáféréshez.

* Az erőforrások elérésére vonatkozó megfelelőségi követelmények.

* További kihívások, például a többtényezős hitelesítés megkövetelése adott erőforrásokhoz.

* Milyen gyakran vizsgálják felül a hozzáférést, és ha igen, hol lesznek dokumentálva.

Ezt a típusú irányítási tervet is el lehet végezni a belső hozzáféréshez is.

## <a name="document-sign-in-conditions-for-external-users"></a>A külső felhasználókra vonatkozó bejelentkezési feltételek dokumentálása.

A csomag részeként meg kell határoznia a külső felhasználók bejelentkezési követelményeit az erőforrásokhoz való hozzáférés során. A bejelentkezési követelmények gyakran az erőforrások kockázati profiljától, valamint a felhasználók bejelentkezésének kockázatértékelésén alapulnak.

A bejelentkezési feltételek az [Azure ad feltételes hozzáférésben](../conditional-access/overview.md) vannak konfigurálva, és feltételből és eredményből állnak. Ha például a többtényezős hitelesítés megkövetelése

**Erőforrás-kockázaton alapuló bejelentkezési feltételek.**

| Alkalmazás kockázati profilja| Vegye fontolóra ezeket a szabályzatokat a többtényezős hitelesítés elindításához |
| - |-|
| Alacsony kockázat| MFA megkövetelése adott alkalmazás-készletekhez |
| Med-kockázat| MFA megkövetelése, ha más kockázatok is fennállnak |
| Magas kockázat| Többtényezős hitelesítés megkövetelése külső felhasználók számára |


[A többtényezős hitelesítés jelenleg a bérlőben lévő B2B-felhasználókra is kényszeríthető](../external-identities/b2b-tutorial-require-mfa.md). 

**Felhasználó-és eszköz-alapú bejelentkezési feltételek**.

| Felhasználói vagy bejelentkezési kockázat| Tekintse meg ezeket a szabályzatokat |
| - | - |
| Eszköz| Megfelelő eszközök megkövetelése |
| Mobilalkalmazások| Jóváhagyott alkalmazások megkövetelése |
| Az Identity Protection magas kockázatot mutat| Jelszó módosításának megkövetelése a felhasználótól |
| Hálózati hely| Egy adott IP-címtartomány és a szigorúan bizalmas projektek közötti bejelentkezés megkövetelése |

Ma az eszköz állapotának a Szabályzathoz való bemenetként való használatához az eszközt regisztrálni kell, vagy csatlakoztatni kell a bérlőhöz. 

Az [Identity Protection kockázati alapú házirendjei](../conditional-access/howto-conditional-access-policy-risk.md) használhatók. A problémákat azonban enyhíteni kell a felhasználó otthoni bérlője számára.

A [hálózati telephelyek](../conditional-access/howto-conditional-access-policy-location.md)esetében korlátozhatja az Ön tulajdonában lévő bármely IP-címtartomány elérését. Ezt akkor érdemes használni, ha csak külső partnereknek szeretne hozzáférni egy alkalmazáshoz, amíg a szervezeten belül vannak.

[További információ a feltételes hozzáférési szabályzatokról](../conditional-access/overview.md).

## <a name="document-access-review-policies"></a>Dokumentum-hozzáférési felülvizsgálati szabályzatok

Dokumentálja az üzleti szabályzatait, amikor át kell tekintenie az erőforrásokhoz való hozzáférést, és ha el kell távolítania a fiókhoz való hozzáférést a külső felhasználók számára. A következő döntésekbe való bemenetek a következők lehetnek:

* A megfelelőségi keretrendszerekben részletezett követelmények.

* Belső üzleti szabályzatok és folyamatok

* Felhasználói viselkedés

Noha a szabályzatok az Ön igényeinek megfelelően testre szabhatók, vegye figyelembe a következőket:

* **Jogosultságok kezelése – hozzáférési felülvizsgálatok**. A jogosultságok kezelése funkciójának használata a következőhöz:

   * A [hozzáférési csomagok automatikusan lejárnak](../governance/entitlement-management-access-package-lifecycle-policy.md), így a külső felhasználók hozzáférhetnek a mellékelt erőforrásokhoz.

   * Adja meg a hozzáférési felülvizsgálatok [szükséges felülvizsgálati gyakoriságát](../governance/entitlement-management-access-reviews-create.md) .

   * Ha [csatlakoztatott szervezeteket](../governance/entitlement-management-organization.md) használ az összes felhasználó egyetlen partnertől való csoportosításához, akkor rendszeres felülvizsgálatokat ütemezhet a vállalat tulajdonosával és a partner képviselőjével.

* **Microsoft 365 csoportok**. Adja meg a [csoport lejárati szabályzatát](/microsoft-365/solutions/microsoft-365-groups-expiration-policy) azon Microsoft 365 csoportok esetében, amelyekhez a külső felhasználókat meghívja. 

* **Egyéb beállítások**. Ha a külső felhasználók jogosultság-kezelési hozzáférési csomagokon vagy Microsoft 365-csoportokon kívül férnek hozzá, az üzleti folyamat beállításával tekintse át a fiókok inaktív vagy törölt állapotának ellenőrzését. Például:

   * Az 90 napig nem bejelentkezett fiókokra vonatkozó bejelentkezési képesség eltávolítása.

   * Mérje fel a hozzáférési igényeket, és tegyen lépéseket minden projekt végén a külső felhasználókkal.

 

## <a name="determine-your-access-control-methods"></a>A hozzáférés-vezérlési módszerek meghatározása

Most, hogy tudja, mit szeretne vezérelni, hogyan szeretné szabályozni az eszközöket a közös hozzáféréshez, valamint a szükséges bejelentkezési és hozzáférési felülvizsgálati szabályzatokat, eldöntheti, hogyan hajthatja végre a tervet. 

Bizonyos funkciók, például a [jogosultságok kezelése](../governance/entitlement-management-overview.md), csak prémium szintű Azure ad 2 (P2) licenccel érhetők el. Microsoft 365 E5 és az Office 365 E5 licencek tartalmazzák az Azure AD P2 licenceket. 

A Microsoft 365, az Office 365 és az Azure AD egyéb kombinációi is lehetővé teszik a külső felhasználók felügyeletét. További információ: [Information Protection](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance) .

> [!NOTE]
> A licencek felhasználónként vannak leképezve. Ezért az Azure AD P2 vagy Microsoft 365 E5 szintjén meghatározott felhasználók, például rendszergazdák és üzlettulajdonosok delegált hozzáférés-vezérlést használhatnak, anélkül, hogy a licenceket az összes felhasználó számára engedélyezné. Az első 50 000 külső felhasználó ingyenes. Ha nem engedélyezi a P2-licenceket a többi belső felhasználó számára, nem fogja tudni használni a jogosultság-kezelési funkciókat, például a hozzáférési csomagokat. 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>Az Azure AD P2 és a Microsoft/Office 365 E5 hozzáférésének szabályozása
Az Azure AD P2 és a Microsoft 365 E5 teljes körű biztonsági és irányítási eszközöket biztosít.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>A kiépítés, a bejelentkezés, a hozzáférés áttekintése és a megszüntetés. A félkövérrel szedett bejegyzések előnyben részesített módszerek

| Szolgáltatás| Külső felhasználók kiépítése| A bejelentkezési reqs betartatása.| Hozzáférés áttekintése| Hozzáférés megszüntetése |
| - | - | - | - | - |
| Azure AD B2B-együttműködés| Meghívás e-mailben, OTP, önkiszolgáló| | **Rendszeres felülvizsgálat partnerként**| Fiók eltávolítása<br>Bejelentkezés korlátozása |
| Jogosultságok kezelése| **Felhasználó hozzáadása hozzárendelés vagy önkiszolgáló hozzáférés használatával**| | Hozzáférési felülvizsgálatok|**Hozzáférési csomag lejárta vagy eltávolítása**|
| Office 365-csoportok| | | Csoporttagság áttekintése| Csoport lejárata vagy törlése<br> Eltávolítási űrlap csoportja |
| Azure AD biztonsági csoportok| | **Feltételes hozzáférési szabályzatok** (a külső felhasználókat szükség szerint adja hozzá a biztonsági csoportokhoz)| |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>Hozzáférés az erőforrásokhoz. A félkövérrel szedett bejegyzések előnyben részesített módszerek

|Szolgáltatás | ALKALMAZÁS-& erőforrás-hozzáférés| SharePoint & OneDrive-hozzáférés| Csapatok hozzáférése| E-mail & dokumentumok biztonsága |
| - |-|-|-|-|
| Jogosultságok kezelése| **Felhasználó hozzáadása hozzárendelés vagy önkiszolgáló hozzáférés használatával**| **Hozzáférési csomagok**| **Hozzáférési csomagok**|  |
| Office 365-csoport| | Hozzáférés a csoporthoz tartozó hely (ek) hez (és kapcsolódó tartalomhoz)| Hozzáférés a csoportokhoz tartozó csapatokhoz (és kapcsolódó tartalmakhoz)|  |
| Bizalmassági címkék| | **A hozzáférés manuális és automatikus besorolása és korlátozása**| **A hozzáférés manuális és automatikus besorolása és korlátozása**| **A hozzáférés manuális és automatikus besorolása és korlátozása** |
| Azure AD biztonsági csoportok| **A hozzáférési csomagokhoz nem tartozó feltételes hozzáférési szabályzatok**| | |  |


### <a name="entitlement-management"></a>Jogosultságok kezelése 

A [jogosultsági felügyeleti hozzáférési csomagok](../governance/entitlement-management-access-package-create.md) lehetővé teszik a csoportokhoz, csoportokhoz, alkalmazásokhoz és SharePoint-webhelyekhez való hozzáférés kiépítését és megszüntetését. Meghatározhatja, hogy mely csatlakoztatott szervezetek számára engedélyezett a hozzáférés, hogy az önkiszolgáló kérelmek engedélyezettek-e, és hogy milyen jóváhagyási munkafolyamatokra van szükség (ha van ilyen) a hozzáférés biztosításához. Annak biztosítása érdekében, hogy a hozzáférés ne maradjon a szükségesnél hosszabb ideig, meghatározhatja a lejárati házirendeket és az egyes hozzáférési csomagok hozzáférési felülvizsgálatait. 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>Az Azure AD P1 és a Microsoft/Office 365 E3 elérésének szabályozása
Az Azure AD P1-sel és a Microsoft 365 E3-mel is robusztus irányítás érhető el

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>Kiépítés, bejelentkezés, hozzáférés áttekintése és megszüntetés


|Szolgáltatás | Külső felhasználók kiépítése| Bejelentkezési követelmények betartatása| Hozzáférés áttekintése| Hozzáférés megszüntetése |
| - |-|-|-|-|
| Azure AD B2B-együttműködés| **Meghívás e-mailben, OTP, önkiszolgáló**| Közvetlen B2B-összevonás| **Rendszeres felülvizsgálat partnerként**| Fiók eltávolítása<br>Bejelentkezés korlátozása |
| Microsoft-vagy Office 365-csoportok| | | | A csoport lejárata vagy törlése.<br>Eltávolítás a csoportból. |
| Biztonsági csoportok| | **Külső felhasználók hozzáadása biztonsági csoportokhoz (szervezeti egység, csapat, projekt stb.)**| |  |
| Feltételes hozzáférési szabályzatok| | **Bejelentkezési feltételes hozzáférési szabályzatok külső felhasználók számára**| |  |


 ### <a name="access-to-resources"></a>Hozzáférés az erőforrásokhoz.

|Szolgáltatás | ALKALMAZÁS-& erőforrás-hozzáférés| SharePoint & OneDrive-hozzáférés| Csapatok hozzáférése| E-mail & dokumentumok biztonsága |
| - |-|-|-|-|
| Microsoft-vagy Office 365-csoportok| | **Hozzáférés a (z) csoporthoz (és a kapcsolódó tartalomhoz) tartozó helyekhez**|**Hozzáférés a Microsoft 365 csoporthoz (és a kapcsolódó tartalomhoz) tartozó csapatokhoz**|  |
| Bizalmassági címkék| | A hozzáférés manuális osztályozása és korlátozása| A hozzáférés manuális besorolása és korlátozása.| Manuális besorolás a korlátozáshoz és a titkosításhoz |
| Feltételes hozzáférési szabályzatok| A hozzáférés-vezérlésre vonatkozó feltételes hozzáférési szabályzatok| | |  |
| További módszerek| | A SharePoint-webhely hozzáférését a biztonsági csoportokkal való részletességgel korlátozhatja.<br>Közvetlen megosztás letiltása.| **Külső meghívók korlátozása a csapatokból**|  |


### <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az erőforrásokhoz való külső hozzáférés biztonságossá tételéhez. Javasoljuk, hogy a felsorolt sorrendben végezze el a műveleteket.

1. [A külső hozzáférés biztonsági helyzetének meghatározása](1-secure-access-posture.md)

2. [Aktuális állapot felderítése](2-secure-access-current-state.md)

3. [Hozzon létre egy irányítási tervet](3-secure-access-plan.md) (itt van.)

4. [Biztonsági csoportok használata](4-secure-access-groups.md)

5. [Áttérés az Azure AD B2B-re](5-secure-access-b2b.md)

6. [Biztonságos hozzáférés a jogosultságok kezelésével](6-secure-access-entitlement-managment.md)

7. [Biztonságos hozzáférés feltételes hozzáférési szabályzatokkal](7-secure-access-conditional-access.md)

8. [Biztonságos hozzáférés érzékenységi címkékkel](8-secure-access-sensitivity-labels.md)

9. [Biztonságos hozzáférés a Microsoft Teams, a OneDrive és a SharePoint rendszerhez](9-secure-access-teams-sharepoint.md)