---
title: Feltételes hozzáférés hozzáadása felhasználói folyamathoz a Azure AD B2C
description: Megtudhatja, hogyan adhat feltételes hozzáférést a Azure AD B2C felhasználói folyamathoz. Konfigurálja a többtényezős hitelesítés (MFA) beállításait és a feltételes hozzáférési szabályzatokat a felhasználói folyamatokban a szabályzatok betartatása és a kockázatos bejelentkezések szervize érdekében.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0e6a872891f09f60ea963fa783e6f49dc4e94a54
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861854"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Feltételes hozzáférés hozzáadása felhasználói folyamatokhoz a Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A feltételes hozzáférés hozzáadható a Azure Active Directory B2C (Azure AD B2C) felhasználói folyamatokhoz vagy egyéni szabályzatokhoz az alkalmazásokba való kockázatos bejelentkezések kezeléséhez. Azure Active Directory (Azure AD) feltételes hozzáférés az eszköz, amelyet a Azure AD B2C használ a jelek egymáshoz hozása, a döntéshozatal és a szervezeti szabályzatok betartatása érdekében.

![Feltételes hozzáférési folyamat](media/conditional-access-user-flow/conditional-access-flow.png)

A kockázatfelmérés szabályzatfeltételekkel való automatizálása azt jelenti, hogy a kockázatos bejelentkezéseket a rendszer azonnal azonosítja, majd javítja vagy letiltja.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>A szolgáltatás áttekintése

Azure AD B2C kiértékeli az egyes bejelentkezési eseményeket, és gondoskodik arról, hogy a felhasználói hozzáférés megadása előtt minden szabályzati követelmény teljesül. Ebben a **kiértékelési fázisban** a feltételes hozzáférési szolgáltatás kiértékeli az Identity Protection kockázatészlelései által gyűjtött jeleket a bejelentkezési események során. A kiértékelési folyamat eredménye jogcímek halmaza, amelyek jelzik, hogy a bejelentkezést meg kell-e adni vagy blokkolni kell. A Azure AD B2C szabályzat ezeket a jogcímeket használja a felhasználói folyamaton belüli művelethez, például letiltja a hozzáférést, vagy egy adott szervizelési megoldással, például többtényezős hitelesítéssel (MFA) követeli meg a felhasználót. A "Hozzáférés blokkolása" felülbírálja az összes többi beállítást.

::: zone pivot="b2c-custom-policy"
Az alábbi példa egy feltételes hozzáférési technikai profilt mutat be, amely a bejelentkezési fenyegetés kiértékeléséhez használható.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

A következő **szervizelési** fázisban a felhasználónak MFA-val kell szembetenének. A művelet Azure AD B2C értesíti az Identity Protectiont arról, hogy az azonosított bejelentkezési fenyegetést és melyik módszerrel orvosolták. Ebben a példában Azure AD B2C jelzi, hogy a felhasználó sikeresen befejezte a többtényezős hitelesítést. 

::: zone pivot="b2c-custom-policy"

Az alábbi példa egy feltételes hozzáférési technikai profilt mutat be, amely az azonosított fenyegetést orvosolja:

```XML
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

## <a name="components-of-the-solution"></a>A megoldás összetevői

A feltételes hozzáférést a következő összetevők teszik Azure AD B2C:

- **Felhasználói folyamat** vagy **egyéni szabályzat,** amely végigvezeti a felhasználót a bejelentkezési és a regisztráció folyamatán.
- **Feltételes hozzáférési szabályzat,** amely egyesíti a jeleket a döntéshozatal és a szervezeti szabályzatok betartatása érdekében. Amikor egy felhasználó egy Azure AD B2C-szabályzaton keresztül jelentkezik be az alkalmazásba, a feltételes hozzáférési szabályzat Azure AD Identity Protection jeleket használ a kockázatos bejelentkezések azonosításához, és bemutatja a megfelelő javítási műveletet.
- **Regisztrált alkalmazás,** amely a megfelelő felhasználói Azure AD B2C vagy egyéni szabályzathoz irányítja a felhasználókat.
- [TOR Browser](https://www.torproject.org/download/) a kockázatos bejelentkezés szimulálására.

## <a name="service-limitations-and-considerations"></a>Szolgáltatási korlátozások és szempontok

Az Azure AD feltételes hozzáférés használata esetén vegye figyelembe a következőket:

- Az Identity Protection helyi és közösségi identitásokhoz is elérhető, például a Google-hez vagy a Facebookhoz. Közösségi identitások esetében manuálisan aktiválnia kell a feltételes hozzáférést. Az észlelés azért korlátozott, mert a közösségi fiók hitelesítő adatait a külső identitásszolgáltató kezeli.
- A Azure AD B2C bérlőkben csak az Azure AD feltételes hozzáférési [szabályzatok egy](../active-directory/conditional-access/overview.md) része érhető el.


## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Tarifacsomag

Azure AD B2C **P2 prémium** szintű előfizetés szükséges a kockázatos bejelentkezési szabályzatok létrehozásához. **A prémium szintű P1-bérlők** létrehozhatnak hely-, alkalmazás-, felhasználóalapú vagy csoportalapú szabályzatokat. További információkért lásd: A Azure AD B2C [tarifacsomag módosítása.](billing.md#change-your-azure-ad-pricing-tier)

## <a name="prepare-your-azure-ad-b2c-tenant"></a>A Azure AD B2C előkészítése

Feltételes hozzáférési szabályzat hozzáadásához tiltsa le az alapértelmezett biztonsági beállításokat:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza a **Címtár és előfizetés** ikont a portál eszköztárán, majd válassza ki azt a címtárat, amely Azure AD B2C bérlőt.
3. Az **Azure-szolgáltatások alatt** válassza a **Azure AD B2C** lehetőséget. Vagy a keresőmezővel megkeresheti és kiválaszthatja a **Azure AD B2C.**
4. Válassza **a Tulajdonságok,** majd a **Biztonság alapértelmezett beállítások kezelése lehetőséget.**

   ![Az alapértelmezett biztonsági beállítások letiltása](media/conditional-access-user-flow/disable-security-defaults.png)

5. A **Biztonsági alapértelmezések engedélyezése alatt válassza** a Nem **lehetőséget.**

   ![Állítsa az Alapértelmezett biztonsági beállítások engedélyezése kapcsolót Nemértékre.](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat hozzáadása

A feltételes hozzáférési szabályzat hozzárendelések és hozzáférés-vezérlések if-then típusú utasítása. A feltételes hozzáférési szabályzatok együttesen hoznak döntéseket és érvényesítik a szervezeti szabályzatokat. A hozzárendelések közötti logikai operátor *és*. Az egyes hozzárendelések operátora *Vagy*.

![Feltételes hozzáférés-hozzárendelések](media/conditional-access-user-flow/conditional-access-assignments.png)

Feltételes hozzáférési szabályzat hozzáadása:

1. A Azure Portal keresse meg és válassza **a** Azure AD B2C.
1. A **Biztonság alatt** válassza a Feltételes hozzáférés **(előzetes verzió) lehetőséget.** Megnyílik **a Feltételes hozzáférési szabályzatok** lap.
1. Válassza **az + Új szabályzat lehetőséget.**
1. Adjon nevet a szabályzatnak, *például: Kockázatos bejelentkezés blokkolása.*
1. A **Hozzárendelések alatt** válassza a **Felhasználók és csoportok** lehetőséget, majd válassza ki az alábbi támogatott konfigurációk egyikét:

    |Belefoglalás  |Licenc | Jegyzetek  |
    |---------|---------|---------|
    |**Minden felhasználó** | P1, P2 |Ha a Minden felhasználó lehetőséget **választja,** ez a szabályzat az összes felhasználót érinti. Ha biztos szeretne lenni abban, hogy nem zárja ki magát, zárja  ki a rendszergazdai fiókját. Válassza a **Kizárás,** majd a Címtárszerepkomókok, **majd** a globális rendszergazda listában való kiválasztását. A Felhasználók és csoportok lehetőséget **is kiválaszthatja,** majd kiválaszthatja a fiókját a **Kizárt felhasználók kiválasztása listában.**  | 
 
1. Válassza **a Felhőalkalmazások vagy műveletek,** majd az **Alkalmazások kiválasztása lehetőséget.** Keresse meg a [függő fél alkalmazását.](tutorial-register-applications.md)

1. Válassza **a Feltételek** lehetőséget, majd válasszon az alábbi feltételek közül. Válassza például a **Bejelentkezési kockázat és** a **Magas,** **Közepes** és **Alacsony** kockázati szintek lehetőséget.
    
    |Feltétel  |Licenc  |Jegyzetek  |
    |---------|---------|---------|
    |**Felhasználói kockázat**|P2|A felhasználói kockázat annak a valószínűségét jelenti, hogy egy adott identitás vagy fiók biztonsága sérül.|
    |**Bejelentkezési kockázat**|P2|A bejelentkezési kockázat annak a valószínűségét jelenti, hogy az identitás tulajdonosa nem engedélyezi egy adott hitelesítési kérést.|
    |**Eszközplatformok**|Nem támogatott| Az eszközön futó operációs rendszer jellemzi. További információ: [Eszközplatformok.](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms)|
    |**Helyek**|P1, P2|A nevestű helyek közé tartozhatnak a nyilvános IPv4-hálózati információk, az ország vagy régió, illetve az ismeretlen területek, amelyek nincsenek leképezve adott országokra vagy régiókra. További információ: [Helyek.](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations) |
 
1. A **Hozzáférés-vezérlés** alatt válassza ki az **Engedélyezés** elemet. Ezután válassza ki, hogy letiltja vagy megadja a hozzáférést:
    
    |Beállítás  |Licenc |Megjegyzés  |
    |---------|---------|---------|
    |**Fájlhozzáférés**|P1, P2| A feltételes hozzáférési szabályzatban megadott feltételek alapján megakadályozza a hozzáférést.|
    |**Hozzáférés engedélyezése a** **Többtényezős hitelesítés megkövetelve használatával**|P1, P2|A feltételes hozzáférési szabályzatban megadott feltételek alapján a felhasználónak egy többtényezős Azure AD B2C kell átesni.|

1. A **Szabályzat engedélyezése alatt** válasszon egyet a következők közül:
    
    |Beállítás  |Licenc |Megjegyzés  |
    |---------|---------|---------|
    |**Csak jelentés**|P1, P2| A csak jelentés lehetővé teszi a rendszergazdák számára, hogy kiértékeljék a feltételes hozzáférési szabályzatok hatását, mielőtt engedélyezték volna őket a környezetben. Javasoljuk, hogy ellenőrizze a szabályzatot ezzel az állapotmal, és határozza meg a végfelhasználókra gyakorolt hatást anélkül, hogy többtényezős hitelesítésre lenne szükség, vagy blokkolni szeretné a felhasználókat. További információ: A feltételes [hozzáférés eredményeinek áttekintése a naplózási jelentésben](#review-conditional-access-outcomes-in-the-audit-report)|
    | **Be**| P1, P2| A hozzáférési szabályzat kiértékelése és kényszerítése nem történik meg. |
    | **Kikapcsolva** | P1, P2| A hozzáférési szabályzat nincs aktiválva, és nincs hatással a felhasználókra. |

1. Engedélyezze a teszt feltételes hozzáférési szabályzatát a Létrehozás **lehetőség kiválasztásával.**

## <a name="add-conditional-access-to-a-user-flow"></a>Feltételes hozzáférés hozzáadása felhasználói folyamathoz

Miután hozzáadta az Azure AD feltételes hozzáférési szabályzatot, engedélyezze a feltételes hozzáférést a felhasználói folyamaton vagy az egyéni szabályzaton. A feltételes hozzáférés engedélyezésekor nem kell megadnia a szabályzat nevét.

Egy adott felhasználóra bármikor több feltételes hozzáférési szabályzat is vonatkozhat. Ebben az esetben a legszigorúbb hozzáférés-vezérlési szabályzat élvez elsőbbséget. Ha például az egyik szabályzat többtényezős hitelesítést (MFA) igényel, míg a másik letiltja a hozzáférést, a felhasználó blokkolva lesz.

## <a name="conditional-access-template-1-sign-in-risk-based-conditional-access"></a>1. feltételes hozzáférés sablon: Bejelentkezési kockázatalapú feltételes hozzáférés

A legtöbb felhasználó viselkedése normális, amely követhető, és amikor eltérnek a normálistól, kockázatos lehet engedni, hogy egyszerűen bejelentkezzenek. Előfordulhat, hogy szeretné letiltani ezt a felhasználót, vagy csak megkérni, hogy végezzen többtényezős hitelesítést annak bizonyítására, hogy valóban azok, akiknek mondja magát.

A bejelentkezési kockázat annak a valószínűségét jelenti, hogy az identitás tulajdonosa nem engedélyezi egy adott hitelesítési kérést. A P2 licenccel rendelkező szervezetek feltételes hozzáférési szabályzatokat hozhatnak létre, amelyek Azure AD Identity Protection [kockázatészlelést tartalmaznak.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk) Vegye figyelembe [a B2C Identity Protection-észlelésére vonatkozó korlátozásokat.](https://docs.microsoft.com/azure/active-directory-b2c/identity-protection-investigate-risk?pivots=b2c-user-flow#service-limitations-and-considerations)

Kockázat észlelésekor a felhasználók többtényezős hitelesítést hajthat végre az önkiszolgáló megoldás érdekében, és bezárhatják a kockázatos bejelentkezési eseményeket, hogy megelőzzék a felesleges zajt a rendszergazdák számára.

A szervezeteknek az alábbi lehetőségek egyikét kell választaniuk a többtényezős hitelesítést (MFA) megkövetelő bejelentkezési kockázatalapú feltételes hozzáférési szabályzat engedélyezéséhez, ha a bejelentkezési kockázat közepes vagy magas.

### <a name="enable-with-conditional-access-policy"></a>Engedélyezés feltételes hozzáférési szabályzatokkal

1. Jelentkezzen be az **Azure Portalra**.
2. Lépjen a **Azure AD B2C**  >  **feltételes**  >  **hozzáférésének eléréséhez.**
3. Válassza az **Új szabályzat lehetőséget.**
4. Nevezze el a szabályzatot. Javasoljuk, hogy a szervezetek hozzanak létre egy jelentéssel bíró szabványt a szabályzataik neveihez.
5. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget.
   1. A **Include (Tartalmazza)** alatt válassza a **Minden felhasználó lehetőséget.**
   2. A **Kizárás alatt** válassza a Felhasználók és **csoportok** lehetőséget, és válassza ki a szervezet vészelérési vagy vészhelyzeti fiókjait. 
   3. Válassza a **Kész** lehetőséget.
6. A **Felhőalkalmazások vagy műveletek** Tartalmazza alatt  >  **válassza** a **Minden felhőalkalmazás lehetőséget.**
7. A **Feltételek Bejelentkezési** kockázat alatt  >  **állítsa** a **Konfigurálás beállítását** Igen **beállításra.** A **Bejelentkezési kockázati szint kiválasztása alatt ez a szabályzat érvényes lesz a következőre:** 
   1. Válassza **a Magas és** a Közepes **lehetőséget.**
   2. Válassza a **Kész** lehetőséget.
8. A **Hozzáférés-vezérlés**  >  **alatt válassza** a Hozzáférés **engedélyezése** lehetőséget, a **Többtényezős hitelesítés** megkövetelve lehetőséget, majd a Kijelölés **lehetőséget.**
9. Erősítse meg a beállításokat, és **állítsa a Szabályzat engedélyezése beállítást** **Bekapcsolva beállításra.**
10. A **szabályzat engedélyezéséhez** válassza a Létrehozás lehetőséget.

### <a name="enable-with-conditional-access-apis"></a>Engedélyezés feltételes hozzáférési API-okkal

Bejelentkezési kockázatalapú feltételes hozzáférési szabályzat feltételes hozzáférési API-okkal való létrehozásához tekintse meg a feltételes hozzáférési [API-k dokumentációját.](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-apis#graph-api)

Az alábbi sablonnal feltételes hozzáférési szabályzatot hozhat létre "CA002: MFA szükséges közepes+ bejelentkezési kockázathoz" megjelenített névvel, csak jelentési módban.

```json
{
    "displayName": "Template 1: Require MFA for medium+ sign-in risk",
    "state": "enabledForReportingButNotEnforced",
    "conditions": {
        "signInRiskLevels": [ "high" ,
            "medium"
        ],
        "applications": {
            "includeApplications": [
                "All"
            ]
        },
        "users": {
            "includeUsers": [
                "All"
            ],
            "excludeUsers": [
                "f753047e-de31-4c74-a6fb-c38589047723"
            ]
        }
    },
    "grantControls": {
        "operator": "OR",
        "builtInControls": [
            "mfa"
        ]
    }
}
```

## <a name="enable-multi-factor-authentication-optional"></a>Többtényezős hitelesítés engedélyezése (nem kötelező)

Amikor feltételes hozzáférést ad egy felhasználói folyamathoz, fontolja meg a **Többtényezős hitelesítés (MFA) használatát.** A felhasználók használhatnak egy egyszeres kódot SMS-ben vagy hangon keresztül, vagy egy e-mailes jelszót a többtényezős hitelesítéshez. Az MFA-beállítások függetlenek a feltételes hozzáférési beállításoktól. Az MFA-t **Always On (Mindig** be) állapotra állíthatja, így a feltételes hozzáférés beállításától függetlenül mindig szükség van az MFA-hitelesítésre. Az MFA-t feltételesre is beállíthatja, így az MFA csak akkor szükséges, ha egy aktív feltételes hozzáférési szabályzat megköveteli. 

> [!IMPORTANT]
> Ha a feltételes hozzáférési szabályzat MFA-val biztosít hozzáférést, de a felhasználó nem regisztrált telefonszámot, előfordulhat, hogy a felhasználó le lesz tiltva.

::: zone pivot="b2c-user-flow"

Ha engedélyezni szeretné a feltételes hozzáférést egy felhasználói folyamathoz, győződjön meg arról, hogy a verzió támogatja a feltételes hozzáférést. Ezek a felhasználói folyamatverziók Ajánlott címkével **vannak megcímkézve.**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza a **Címtár és előfizetés** ikont a portál eszköztárán, majd válassza ki azt a címtárat, amely a Azure AD B2C bérlőt.

1. Az **Azure-szolgáltatások alatt** válassza a **Azure AD B2C** lehetőséget. Vagy a keresőmezővel megkeresheti és kiválaszthatja a **Azure AD B2C.**

1. A **Szabályzatok alatt** válassza a **Felhasználói folyamatok lehetőséget.** Ezután válassza ki a felhasználói folyamatot.

1. Válassza **a Tulajdonságok** lehetőséget, és ellenőrizze, hogy a felhasználói folyamat támogatja-e a feltételes hozzáférést a Feltételes hozzáférés **beállítással.**
 
   ![Az MFA és a feltételes hozzáférés konfigurálása a Tulajdonságok között](media/conditional-access-user-flow/add-conditional-access.png)

1. A **Többtényezős hitelesítés** szakaszban válassza ki a kívánt **MFA-módszert,** majd az **MFA** kényszerítése területen válassza a **Feltételes (ajánlott) lehetőséget.**
 
1. A Feltételes **hozzáférés szakaszban** jelölje be a Feltételes **hozzáférési szabályzatok kikényszeríteni jelölőnégyzetet.**

1. Kattintson a **Mentés** gombra.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Feltételes hozzáférés hozzáadása a szabályzathoz

1. Példa egy feltételes hozzáférési szabályzatra a [GitHubon.](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access)
1. Minden fájlban cserélje le a sztringet a bérlő Azure AD B2C `yourtenant` nevére. Ha például a B2C-bérlő neve *contosob2c,* a összes példánya `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` lesz.
1. Töltse fel a szabályzatfájlokat.

## <a name="test-your-custom-policy"></a>Az egyéni szabályzat tesztelése

1. Válassza az `B2C_1A_signup_signin_with_ca` vagy `B2C_1A_signup_signin_with_ca_whatif` a szabályzatot az áttekintő oldal megnyitásához. Ezután válassza **a Felhasználói folyamat futtatása lehetőséget.** Az **Alkalmazás alatt** válassza a *webapp1 lehetőséget.* A **válasz URL-címnek** a következőnek kell `https://jwt.ms` lennie: .
1. Másolja a Felhasználói folyamat **végpontjának futtatása alatti URL-címet.**

1. Kockázatos bejelentkezés szimulálása érdekében nyissa meg a [Tor Browsert,](https://www.torproject.org/download/) és használja az előző lépésben kimásott URL-címet a regisztrált alkalmazásba való bejelentkezéshez.

1. Adja meg a kért adatokat a bejelentkezési oldalon, majd próbáljon meg bejelentkezni. A jogkivonat vissza lesz küldve a következőnek: , `https://jwt.ms` és meg kell jelenni Önnek. A jwt.ms tokenben azt kell látnia, hogy a bejelentkezés le lett tiltva.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a létrehozott felhasználói folyamatot az áttekintő oldal megnyitásához, majd válassza a **Felhasználói folyamat futtatása lehetőséget.** Az **Alkalmazás alatt** válassza a *webapp1 lehetőséget.* A **válasz URL-címnek** a következőnek kell `https://jwt.ms` lennie: .

1. Másolja a Felhasználói folyamat **végpontjának futtatása alatti URL-címet.**

1. Kockázatos bejelentkezés szimulálása érdekében nyissa meg a [Tor Browsert,](https://www.torproject.org/download/) és használja az előző lépésben kimásott URL-címet a regisztrált alkalmazásba való bejelentkezéshez.

1. Adja meg a kért adatokat a bejelentkezési oldalon, majd próbáljon meg bejelentkezni. A jogkivonat vissza lesz küldve a `https://jwt.ms` következőbe: , és meg kell jelenni Önnek. A jwt.ms tokenben látnia kell, hogy a bejelentkezés le van tiltva.

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>A feltételes hozzáférés eredményeinek áttekintése a naplózási jelentésben

Feltételes hozzáférési esemény eredményének áttekintése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza a **Címtár és előfizetés** ikont a portál eszköztárán, majd válassza ki azt a címtárat, amely a Azure AD B2C bérlőt.

3. Az **Azure-szolgáltatások alatt** válassza a **Azure AD B2C** lehetőséget. Vagy a keresőmezővel megkeresheti és kiválaszthatja a **Azure AD B2C.**

4. A **Tevékenységek alatt** válassza az **Auditnaplók lehetőséget.**

5. Szűrje az auditnaplót a **Category** (Kategória) **B2C (B2C)** és a Activity Resource Type (Tevékenység **erőforrástípusa)** **identityProtection (Identitás védelme) beállításával.** Ezután válassza az **Alkalmaz** lehetőséget.

6. Tekintse át a naplózási tevékenységet az elmúlt hét napra. A következő tevékenységtípusok tartoznak ide:

   - **Feltételes hozzáférési szabályzatok értékelése:** Ez az auditnapló-bejegyzés azt jelzi, hogy a feltételes hozzáférés kiértékelése a hitelesítés során történt.
   - **Felhasználó szervizelése:** Ez a bejegyzés azt jelzi, hogy a végfelhasználó megfelelt egy feltételes hozzáférési szabályzat követelményeknek, és ez a tevékenység jelentve lett a kockázati motornak a felhasználó mérséklése (a kockázat csökkentése) érdekében.

7. Válassza **ki** a listában a Feltételes hozzáférési szabályzat kiértékelése naplóbejegyzést a Tevékenység **részletei:** Auditnapló oldal megnyitásához, amely megjeleníti az auditnapló azonosítóit, valamint az alábbi információkat a **További részletek szakaszban:**

   - **ConditionalAccessResult**: A feltételes szabályzat kiértékelése által megkövetelt engedély.
   - **AppliedPolicies**: Azon feltételes hozzáférési szabályzatok listája, amelyekben a feltételek teljesültek, és a szabályzatok BE vannak stb.
   - **ReportingPolicies:** Azon feltételes hozzáférési szabályzatok listája, amelyek csak jelentési módra voltak beállítva, és amelyekben teljesülnek a feltételek.

## <a name="next-steps"></a>Következő lépések

[Felhasználói felület testreszabása Azure AD B2C felhasználói folyamatában](customize-ui-with-html.md)
