---
title: A jogkivonatok konfigurálható élettartama
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan állíthatja be a Microsoft identitásplatformja által kiadott hozzáférési, SAML- és azonosító-jogkivonatok élettartamát.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e1753391c7b61b6e9bd9e6ac0d142b4ee94502d8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363971"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>A jogkivonatok konfigurálható élettartama a Microsoft identitásplatformján (előzetes verzió)

Megadhatja a Microsoft identitásplatformja által kiadott hozzáférési, azonosítói vagy SAML-jogkivonat élettartamát. Beállíthatja a cégen belüli összes alkalmazás jogkivonatának élettartamát több-bérlős alkalmazások (több cég) vagy munkahelyen belüli adott szolgáltatásnév esetén. Jelenleg azonban nem támogatjuk a jogkivonatok élettartamának konfigurálását a [felügyelt identitás szolgáltatásnévihez.](../managed-identities-azure-resources/overview.md)

Az Azure AD-ban a szabályzatobjektumok olyan szabályok halmazát jelölik, amelyek az egyes alkalmazásokon vagy a szervezet összes alkalmazásán érvényesítve vannak. Minden házirendtípus egyedi struktúrával rendelkezik, és olyan tulajdonságokkal rendelkezik, amelyek olyan objektumokra vannak alkalmazva, amelyekhez hozzá vannak rendelve.

Kijelölhet egy szabályzatot a szervezet alapértelmezett szabályzataként. A szabályzat a szervezet bármely alkalmazásában érvényes, ha nem bírálja felül egy magasabb prioritású házirend. A szabályzatokat adott alkalmazásokhoz is hozzárendelheti. A prioritás sorrendje szabályzattípusonként változik.

Példákért olvassa el a [jogkivonatok élettartamának konfigurálásán keresztüli példákat.](configure-token-lifetimes.md)

> [!NOTE]
> A konfigurálható jogkivonat-élettartam házirend csak a SharePoint Online-hoz és OneDrive Vállalati verzió-erőforrásokhoz hozzáférő mobil- és asztali ügyfelekre vonatkozik, webböngésző-munkamenetekre nem.
> A SharePoint Online és a OneDrive Vállalati verzió böngésző-munkamenetek élettartamának kezeléséhez használja a Feltételes hozzáférés [munkamenet-élettartam](../conditional-access/howto-conditional-access-session-lifetime.md) funkciót. A tétlen munkamenet-időtúllépések konfigurálásával kapcsolatos további információkért tekintse meg a [SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) blogját.

## <a name="license-requirements"></a>Licenckövetelmények

A funkció használatához P1 prémium szintű Azure AD szükséges. A követelményeknek megfelelő licencet az Ingyenes és Prémium kiadások általánosan elérhető funkcióinak összehasonlítása oldalon [találja.](https://azure.microsoft.com/pricing/details/active-directory/)

A Microsoft 365 [licenccel rendelkező](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) ügyfelek is hozzáférhetnek a feltételes hozzáférési funkciókhoz.

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>Hozzáférési, SAML- és azonosító-jogkivonatok jogkivonat-élettartam-szabályzatai

Beállíthatja a hozzáférési jogkivonatok, SAML-jogkivonatok és azonosító-jogkivonatok élettartam-szabályzatát.

### <a name="access-tokens"></a>Hozzáférési jogkivonatok

Az ügyfelek hozzáférési jogkivonatokkal férnek hozzá a védett erőforrásokhoz. A hozzáférési jogkivonatok csak a felhasználó, az ügyfél és az erőforrás adott kombinációjához használhatók. A hozzáférési jogkivonatok nem vonhatók vissza, és a lejáratukig érvényesek. A hozzáférési jogkivonatot bekért rosszindulatú aktorok élettartamuk végéig használhatja őket. A hozzáférési jogkivonatok élettartamának beállítása a rendszer teljesítményének javítása és a felhasználói fiók letiltása után az ügyfél hozzáférés-megőrzési időének növelése között van. A rendszer teljesítményének növelése az ügyfél friss hozzáférési jogkivonatok lekért számának csökkentésével érhető el.  Az alapértelmezett érték a jogkivonatot kérő ügyfélalkalmazástól függően változik. Például a folyamatos hozzáférés-kiértékelést (CAE) lehetővé t tő ügyfelek, amelyek CAE-kompatibilis munkameneteket egyeztetnek, hosszú élettartamú (legfeljebb 28 órás) jogkivonat-élettartamot fognak látni. A jogkivonat lejárta után az ügyfélnek a frissítési jogkivonat használatával (általában csendesen) be kell szereznie egy új frissítési jogkivonatot és hozzáférési jogkivonatot.

### <a name="saml-tokens"></a>SAML-jogkivonatok

Az SAML-jogkivonatokat számos webes SaaS-alkalmazás használja, és Azure Active Directory SAML2 protokollvégpont használatával szerezhetők be. A WS-Federationt használó alkalmazások is használják őket. A jogkivonat alapértelmezett élettartama 1 óra. Egy alkalmazás szempontjából a jogkivonat érvényességi időtartama a jogkivonat elemének NotOnOrAfter értéke `<conditions …>` alapján van meghatározva. A jogkivonat érvényességi idejének lejárta után az ügyfélnek új hitelesítési kérést kell kezdeményeznie, amely az egyszeri bejelentkezés (SSO) munkamenet-jogkivonatának eredményeként gyakran interaktív bejelentkezés nélkül is teljesül.

A NotOnOrAfter értéke a `AccessTokenLifetime` paraméterével `TokenLifetimePolicy` módosítható. Ha van ilyen, a szabályzatban konfigurált élettartamra lesz beállítva, plusz egy öt perces óra-eltúdási tényező.

A elemben megadott NotOnOrAfter tulajdonos-megerősítést a jogkivonat `<SubjectConfirmationData>` élettartamának konfigurációja nem befolyásolja. 

### <a name="id-tokens"></a>Azonosító jogkivonatok

Az azonosító jogkivonatokat a rendszer a webhelyeknek és a natív ügyfeleknek is továbbkűszi. Az azonosító jogkivonatok a felhasználó profilinformációit tartalmazzák. Az azonosító jogkivonat a felhasználó és az ügyfél egy adott kombinációjához van kötve. Az azonosító jogkivonatok érvényesnek minősülnek a lejáratukig. A webalkalmazások általában megegyezik a felhasználó munkamenet-élettartamával az alkalmazásban a felhasználó számára kiadott azonosító jogkivonat élettartamával. Az azonosító jogkivonat élettartamának módosításával szabályozhatja, hogy milyen gyakran járjon le a webalkalmazás az alkalmazás-munkamenet lejártakor, és milyen gyakran van szükség arra, hogy a felhasználót újra hitelesíteni kell a Microsoft identitásplatformján (csendes vagy interaktív módon).

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>Jogkivonatok élettartam-szabályzatai frissítési jogkivonatok és munkamenet-jogkivonatok számára

Nem állíthat be jogkivonat-élettartam-szabályzatokat a frissítési jogkivonatok és a munkamenet-jogkivonatok számára.

> [!IMPORTANT]
> 2021. január 30-án nem konfigurálhatja a frissítési és munkamenet-jogkivonatok élettartamát. Azure Active Directory többé nem tiszteletben tartani a meglévő szabályzatok frissítési és munkamenet-jogkivonat-konfigurációját.  A meglévő jogkivonatok lejárta után kiadott új jogkivonatok mostantól az alapértelmezett [konfigurációra vannak beállítva.](#configurable-token-lifetime-properties) A hozzáférési, SAML- és azonosító-jogkivonatok élettartamát a frissítés és a munkamenet-jogkivonat-konfiguráció kiesése után is konfigurálhatja.
>
> A meglévő jogkivonat élettartama nem változik. A lejáratuk után egy új jogkivonat lesz kibocsátva az alapértelmezett érték alapján.
>
> Ha továbbra is meg kell határoznia azt az időszakot, amely után a rendszer megkéri a felhasználót, hogy jelentkezzen be újra, konfigurálja a feltételes hozzáférés bejelentkezési gyakoriságát. További információ a feltételes hozzáférésről: [Hitelesítési munkamenet-kezelés konfigurálása feltételes hozzáféréssel.](../conditional-access/howto-conditional-access-session-lifetime.md)

## <a name="configurable-token-lifetime-properties"></a>A jogkivonatok élettartamának konfigurálható tulajdonságai
A jogkivonatok élettartam-szabályzata a szabályzatobjektumok olyan típusa, amely a jogkivonatok élettartamára vonatkozó szabályokat tartalmaz. Ez a szabályzat szabályozza, hogy az erőforráshoz szükséges hosszú hozzáférési, SAML- és azonosító-jogkivonatok mennyi ideig minősülnek érvényesnek. A jogkivonatok élettartam-szabályzatai nem beállíthatók frissítési és munkamenet-jogkivonatok számára. Ha nincs beállított szabályzat, a rendszer az alapértelmezett élettartamértéket kényszeríti ki.

### <a name="access-id-and-saml2-token-lifetime-policy-properties"></a>Hozzáférési, azonosítói és SAML2-jogkivonat élettartamának szabályzattulajdonságai

A Hozzáférési jogkivonat élettartama tulajdonság csökkentésével csökkenthető annak a kockázata, hogy egy rosszindulatú szereplő hosszabb ideig használja a hozzáférési jogkivonatot vagy azonosító jogkivonatot. (Ezeket a jogkivonatokat nem lehet visszavonni.) Ezzel az a probléma is negatívan befolyásolja a teljesítményt, mert a jogkivonatokat gyakrabban kell cserélni.

Példa: Szabályzat létrehozása [webes bejelentkezéshez.](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)

A hozzáférés, az azonosító és az SAML2-jogkivonat konfigurációjára a következő tulajdonságok és a megfelelő értékek vonatkoznak:

- **Tulajdonság:** Hozzáférési jogkivonat élettartama
- **Szabályzattulajdonság-sztring:** AccessTokenLifetime
- **Érintett adatok:** Hozzáférési jogkivonatok, azonosító jogkivonatok, SAML2-jogkivonatok
- **Alapértelmezett:**
    - Hozzáférési jogkivonatok: a jogkivonatot kérő ügyfélalkalmazástól függően változó. Például a folyamatos hozzáférés-kiértékelést (CAE) lehetővé t tő ügyfelek, amelyek CAE-kompatibilis munkameneteket egyeztetnek, hosszú élettartamú (legfeljebb 28 órás) jogkivonat-élettartamot fognak látni.
    - Azonosító jogkivonatok, SAML2-jogkivonatok: 1 óra
- **Minimum:** 10 perc
- **Maximum:** 1 nap

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>A frissítési és munkamenet-jogkivonatok élettartam-szabályzatának tulajdonságai

A frissítésre és a munkamenet-jogkivonat konfigurálásra az alábbi tulajdonságok és a megfelelő értékek vonatkoznak. A frissítési és munkamenet-jogkivonatok 2021. január 30-i kiesése után az Azure AD csak az alább ismertetett alapértelmezett értékeket fogja becslni. Ha úgy dönt, [](../conditional-access/howto-conditional-access-session-lifetime.md) hogy nem használja a feltételes hozzáférést a bejelentkezési gyakoriság kezeléséhez, a frissítési és munkamenet-jogkivonatok ezen a napon az alapértelmezett konfigurációra lesznek beállítva, és a továbbiakban nem módosíthatja az élettartamukat.  

|Tulajdonság   |Szabályzattulajdonság sztring    |Befolyásolja |Alapértelmezett |
|----------|-----------|------------|------------|
|Frissítési jogkivonat maximális inaktív ideje |MaxInactiveTime (MaxInactiveTime)  |Jogkivonatok frissítése |90 nap  |
|Single-Factor token maximális kora  |MaxAgeSingleFactor  |Jogkivonatok frissítése (bármely felhasználó számára)  |Visszavonásig  |
|Többtényezős frissítési jogkivonat maximális kora  |MaxAgeMultiFactor  |Jogkivonatok frissítése (bármely felhasználó számára) |Visszavonásig  |
|Single-Factor-jogkivonat maximális kora  |MaxAgeSessionSingleFactor |Munkamenet-jogkivonatok (állandó és nem perzisztens)  |Visszavonásig |
|Többtényezős munkamenet jogkivonatának maximális kora  |MaxAgeSessionMultiFactor  |Munkamenet-jogkivonatok (állandó és nem perzisztens)  |Visszavonásig |

A PowerShell használatával megkeresheti a kieső szabályzatokat.  A [PowerShell-parancsmagokkal](configure-token-lifetimes.md#get-started) a szervezetben létrehozott összes szabályzatot láthatja, vagy megkeresi, hogy mely alkalmazások és szolgáltatásnévek kapcsolódnak egy adott szabályzathoz.

## <a name="policy-evaluation-and-prioritization"></a>Szabályzatok kiértékelése és rangsorolása
Létrehozhat, majd hozzárendelhet egy jogkivonat élettartam-szabályzatot egy adott alkalmazáshoz, a szervezethez és a szolgáltatásnévhez. Egy adott alkalmazásra több szabályzat is vonatkozhat. A jogkivonatok életbe lépő élettartam-szabályzata a következő szabályokat követi:

* Ha egy szabályzat explicit módon hozzá van rendelve a szolgáltatásnévhez, a rendszer kényszeríti azt.
* Ha nincs explicit módon hozzárendelve szabályzat a szolgáltatásnévhez, a rendszer kényszeríti a szolgáltatásnév szülőszervezetéhez rendelt szabályzatot.
* Ha nincs explicit módon hozzárendelve szabályzat a szolgáltatásnévhez vagy a szervezethez, az alkalmazáshoz rendelt szabályzat kényszerítve lesz.
* Ha nincs hozzárendelve szabályzat a szolgáltatásnévhez, a szervezethez vagy az alkalmazásobjektumhoz, a rendszer kényszeríti az alapértelmezett értékeket. (Lásd a táblázatot [a Configurable token lifetime properties (Konfigurálható jogkivonatok élettartamának tulajdonságai) dokumentumban.)](#configurable-token-lifetime-properties)

További információ az alkalmazásobjektumok és a szolgáltatásnév-objektumok közötti kapcsolatról: Alkalmazás- és szolgáltatásnév-objektumok a [Azure Active Directory.](app-objects-and-service-principals.md)

A jogkivonat érvényességének kiértékelése a jogkivonat használatakor történik. Az elért alkalmazás legmagasabb prioritású szabályzata lép életbe.

Az itt használt összes időformátum a C# [TimeSpan](/dotnet/api/system.timespan) objektumnak megfelelően van formázva – D.HH:MM:SS.  Tehát a 80 nap és a 30 perc a . `80.00:30:00`  A kezdő D nullával eldobható, így a 90 perc a `00:90:00` következő: .  

## <a name="cmdlet-reference"></a>Parancsmag-referencia

Ezek a gráfelőnézethez Azure Active Directory [PowerShell-modul parancsmagjai.](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#service-principals)

### <a name="manage-policies"></a>A szabályzatok kezelése

A házirendek kezeléséhez használhatja az alábbi parancsmagokat.

| Parancsmag | Leírás | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Új szabályzatot hoz létre. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Lekért minden Azure AD-szabályzatot vagy egy adott szabályzatot. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Lekért minden olyan alkalmazást és szolgáltatásnévt, amely egy szabályzathoz van kapcsolva. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Frissíti a meglévő szabályzatot. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Törli a megadott szabályzatot. |

### <a name="application-policies"></a>Tanúsítványhasználati házirend
Az alábbi parancsmagokat használhatja az alkalmazás-szabályzatok számára.</br></br>

| Parancsmag | Leírás | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | A megadott szabályzatot egy alkalmazáshoz kapcsolódik. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Lekérte az alkalmazáshoz rendelt szabályzatot. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Eltávolít egy szabályzatot egy alkalmazásból. |

### <a name="service-principal-policies"></a>Szolgáltatásnév-szabályzatok
A következő parancsmagokat használhatja a szolgáltatásnév-szabályzatok számára.

| Parancsmag | Leírás | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | A megadott szabályzatot egy szolgáltatásnévhez kapcsolódik. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Lekért minden, a megadott szolgáltatásnévhez kapcsolódó szabályzatot.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Eltávolítja a szabályzatot a megadott szolgáltatásnévből.|

## <a name="next-steps"></a>Következő lépések

További tudnivalókért olvassa el a jogkivonatok élettartamának [konfigurálásán keresztüli példákat.](configure-token-lifetimes.md)
