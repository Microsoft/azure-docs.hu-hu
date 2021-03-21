---
title: Feltételes hozzáférés hozzáadása egy felhasználói folyamathoz Azure AD B2C
description: Megtudhatja, hogyan adhat hozzá feltételes hozzáférést a Azure AD B2C felhasználói folyamatokhoz. A többtényezős hitelesítési (MFA) beállítások és a feltételes hozzáférési házirendek konfigurálása a felhasználói folyamatokban a házirendek betartatása és a kockázatos bejelentkezések szervizelése érdekében.
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
ms.openlocfilehash: 6325a890ea297a3aa2bdad76a1d95c10448a7b61
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033913"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Feltételes hozzáférés hozzáadása a felhasználói folyamatokhoz Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A feltételes hozzáférés a Azure Active Directory B2C (Azure AD B2C) felhasználói folyamatokhoz vagy egyéni szabályzatokhoz vehető fel, hogy a kockázatos bejelentkezéseket az alkalmazásaihoz lehessen kezelni. A Azure Active Directory (Azure AD) feltételes hozzáférés a Azure AD B2C által a jelek összekapcsolására, a döntések meghozatalára és a szervezeti házirendek betartatására használt eszköz.

![Feltételes hozzáférés folyamata](media/conditional-access-user-flow/conditional-access-flow.png)

A kockázatértékelés házirend-feltételekkel való automatizálása azt jelenti, hogy a kockázatos bejelentkezéseket azonnal azonosítják, majd szervizelni vagy letiltják.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>A szolgáltatás áttekintése

Azure AD B2C kiértékeli az egyes bejelentkezési eseményeket, és biztosítja, hogy az összes házirend-követelmény teljesül a felhasználói hozzáférés megadása előtt. A **kiértékelési** fázisban a feltételes hozzáférési szolgáltatás a bejelentkezési események során kiértékeli az Identity Protection kockázati észlelésével gyűjtött jeleket. A kiértékelési folyamat eredménye olyan jogcímek összessége, amelyek jelzik, hogy a bejelentkezést meg kell-e adni vagy le kell tiltani. A Azure AD B2C szabályzat ezeket a jogcímeket használja a felhasználói folyamaton belüli művelet végrehajtására, például blokkolja a hozzáférést, vagy megtámadja a felhasználót egy adott szervizeléssel, például a többtényezős hitelesítéssel (MFA). A "hozzáférés letiltása" felülbírálja az összes többi beállítást.

::: zone pivot="b2c-custom-policy"
Az alábbi példa egy feltételes hozzáférési technikai profilt mutat be, amely a bejelentkezési fenyegetés kiértékelésére szolgál.

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

A következő **szervizelési** fázisban a felhasználó az MFA-val van megtámadva. Ha elkészült, Azure AD B2C tájékoztatja az Identity Protectiont arról, hogy az azonosított bejelentkezési fenyegetés szervizelése megtörtént, és milyen módszerrel. Ebben a példában Azure AD B2C jelzi, hogy a felhasználó sikeresen befejezte a multi-Factor Authentication kérdését. 

::: zone pivot="b2c-custom-policy"

Az alábbi példa egy feltételes hozzáférési technikai profilt mutat be az azonosított fenyegetés szervizeléséhez:

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

Ezek azok az összetevők, amelyek engedélyezik a feltételes hozzáférést a Azure AD B2Cban:

- **Felhasználói folyamat** vagy **egyéni házirend** , amely a felhasználót a bejelentkezési és a regisztrációs folyamaton keresztül irányítja.
- **Feltételes hozzáférési szabályzat** , amely lehetővé teszi a jelek összekapcsolását a döntések meghozatalához és a szervezeti szabályzatok betartatásához. Amikor egy felhasználó egy Azure AD B2C szabályzattal jelentkezik be az alkalmazásba, a feltételes hozzáférési házirend Azure AD Identity Protection jeleket használ a kockázatos bejelentkezések azonosítására, és megadja a megfelelő szervizelési műveletet.
- **Regisztrált alkalmazás** , amely a felhasználókat a megfelelő Azure ad B2C felhasználói folyamatra vagy egyéni házirendre irányítja.
- [Tor-böngésző](https://www.torproject.org/download/) a kockázatos bejelentkezés szimulálása érdekében.

## <a name="service-limitations-and-considerations"></a>A szolgáltatásra vonatkozó korlátozások és megfontolások

Az Azure AD feltételes hozzáférés használata esetén vegye figyelembe a következőket:

- A személyazonosság védelme a helyi és a közösségi identitások, például a Google vagy a Facebook esetében egyaránt elérhető. A közösségi identitások esetében manuálisan kell aktiválni a feltételes hozzáférést. Az észlelés korlátozott, mert a közösségi fiók hitelesítő adatait a külső identitás szolgáltatója kezeli.
- Azure AD B2C bérlők esetében csak az [Azure ad feltételes hozzáférési](../active-directory/conditional-access/overview.md) szabályzatok egy részhalmaza érhető el.


## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Tarifacsomag

Azure AD B2C **prémium P2** szükséges a kockázatos bejelentkezési szabályzatok létrehozásához. A **prémium P1** -bérlők létrehozhatnak olyan házirendet, amely a hely, az alkalmazás, a felhasználó-vagy a csoport alapú házirendek alapján hozható létre. További információ: [a Azure ad B2C árképzési csomag módosítása](billing.md#change-your-azure-ad-pricing-tier)

## <a name="prepare-your-azure-ad-b2c-tenant"></a>A Azure AD B2C bérlő előkészítése

Feltételes hozzáférési szabályzat hozzáadásához tiltsa le a biztonsági beállításokat:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
3. Az **Azure-szolgáltatások** területen válassza a **Azure ad B2C** lehetőséget. Vagy a keresőmező használatával megkeresheti és kiválaszthatja a **Azure ad B2C**.
4. Válassza a **Tulajdonságok** lehetőséget, majd válassza a **biztonsági beállítások kezelése** lehetőséget.

   ![Biztonsági alapértékek letiltása](media/conditional-access-user-flow/disable-security-defaults.png)

5. A **biztonsági Alapértelmezések engedélyezése** területen válassza a **nem** lehetőséget.

   ![A biztonsági Alapértelmezések engedélyezése váltógomb beállítása nem értékre](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat hozzáadása

A feltételes hozzáférési szabályzat a hozzárendelések és hozzáférés-vezérlések if-then utasítása. A feltételes hozzáférési szabályzatok közösen határozzák meg a döntéseket, és kényszerítik a szervezeti szabályzatok betartatását. A hozzárendelések közötti logikai operátor a *és* a. Az egyes hozzárendelésekben szereplő operátor a *vagy* a.

![Feltételes hozzáférési hozzárendelések](media/conditional-access-user-flow/conditional-access-assignments.png)

Feltételes hozzáférési szabályzat hozzáadása:

1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. A **Biztonság** területen válassza a **feltételes hozzáférés (előzetes verzió)** lehetőséget. Megnyílik a **feltételes hozzáférési szabályzatok** lap.
1. Válassza az **+ új házirend** elemet.
1. Adja meg a szabályzat nevét, például tiltsa le a *kockázatos bejelentkezést*.
1. A **hozzárendelések** területen válassza a **felhasználók és csoportok** lehetőséget, majd válassza ki az alábbi támogatott konfigurációk egyikét:

    |Belefoglalás  |Licenc | Jegyzetek  |
    |---------|---------|---------|
    |**Minden felhasználó** | P1, P2 |Ha úgy dönt, hogy az **összes felhasználót** tartalmazza, ez a szabályzat minden felhasználóra hatással lesz. Annak érdekében, hogy ne zárolja magát, zárja ki a rendszergazdai fiókját a **kizárás** elem kiválasztásával, majd a **címtárbeli szerepkörök** kiválasztása lehetőséggel, majd válassza a **globális rendszergazda** elemet a listában. A **felhasználók és csoportok** lehetőséget is kiválaszthatja, majd kiválaszthatja a fiókját a **kizárt felhasználók kiválasztása** listában.  | 
 
1. Válassza a **felhőalapú alkalmazások vagy műveletek** lehetőséget, majd **válassza az alkalmazások lehetőséget**. Tallózással keresse meg a [függő entitás alkalmazását](tutorial-register-applications.md).

1. Válassza a **feltételek** lehetőséget, majd válasszon az alábbi feltételek közül. Válassza például a **bejelentkezési kockázat** és a **magas**, **közepes** és **alacsony** kockázati szintek lehetőséget.
    
    |Feltétel  |Licenc  |Jegyzetek  |
    |---------|---------|---------|
    |**Felhasználói kockázat**|P2|A felhasználói kockázat azt jelzi, hogy egy adott identitás vagy fiók biztonsága sérül.|
    |**Bejelentkezési kockázat**|P2|A bejelentkezési kockázat azt jelzi, hogy egy adott hitelesítési kérelem nem jogosult az identitás tulajdonosa számára.|
    |**Eszközplatformok**|Nem támogatott| Az eszközön futó operációs rendszer jellemzi. További információ: az [eszközök platformja](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms).|
    |**Helyek**|P1, P2|A nevesített helyszínek tartalmazhatják a nyilvános IPv4-hálózati információkat, az országot vagy a régiót, illetve az ismeretlen területeket, amelyek nem képezhetők le adott országokba vagy régiókba. További információ: [Locations](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations). |
 
1. A **Hozzáférés-vezérlés** alatt válassza ki az **Engedélyezés** elemet. Ezután válassza ki, hogy szeretné-e letiltani vagy megadni a hozzáférést:
    
    |Beállítás  |Licenc |Megjegyzés  |
    |---------|---------|---------|
    |**Fájlhozzáférés**|P1, P2| A hozzáférés megakadályozása a feltételes hozzáférési házirendben megadott feltételek alapján.|
    |**Hozzáférés engedélyezése** a **többtényezős hitelesítés megkövetelése**|P1, P2|Az ebben a feltételes hozzáférési házirendben megadott feltételek alapján a felhasználónak Azure AD B2C többtényezős hitelesítéssel kell eljárnia.|

1. A **házirend engedélyezése** területen válassza a következők egyikét:
    
    |Beállítás  |Licenc |Megjegyzés  |
    |---------|---------|---------|
    |**Csak jelentés**|P1, P2| A jelentés csak a rendszergazdák számára lehetővé teszi a feltételes hozzáférési házirendek hatásának kiértékelését, mielőtt engedélyezi őket a környezetében. Javasoljuk, hogy ellenőrizze ezt az állapotot, és határozza meg a végfelhasználók számára a többtényezős hitelesítés vagy a felhasználók blokkolása nélkül gyakorolt hatást. További információ: [feltételes hozzáférési eredmények áttekintése a naplózási jelentésben](#review-conditional-access-outcomes-in-the-audit-report)|
    | **Be**| P1, P2| A hozzáférési házirend ki van értékelve, és nincs kikényszerítve. |
    | **Kikapcsolva** | P1, P2| A hozzáférési házirend nincs aktiválva, és nincs hatással a felhasználókra. |

1. A **Létrehozás** lehetőség kiválasztásával engedélyezheti a feltételes hozzáférési szabályzat tesztelését.

## <a name="add-conditional-access-to-a-user-flow"></a>Feltételes hozzáférés hozzáadása egy felhasználói folyamathoz

Miután hozzáadta az Azure AD feltételes hozzáférési szabályzatot, engedélyezze a feltételes hozzáférést a felhasználói folyamatában vagy az egyéni házirendben. A feltételes hozzáférés engedélyezésekor nem kell megadnia a szabályzat nevét.

Egyszerre több feltételes hozzáférési szabályzat is alkalmazható az egyes felhasználókra. Ebben az esetben a legszigorúbb hozzáférés-vezérlési házirend elsőbbséget élvez. Ha például egy házirendhez többtényezős hitelesítés (MFA) szükséges, míg a másik blokkolja a hozzáférést, a rendszer letiltja a felhasználót.

## <a name="enable-multi-factor-authentication-optional"></a>Multi-Factor Authentication engedélyezése (nem kötelező)

Ha feltételes hozzáférést ad hozzá egy felhasználói folyamathoz, vegye figyelembe a **többtényezős hitelesítés (MFA)** használatát. A felhasználók az SMS-ben és a hangon is használhatnak egyszeri kódot, vagy egy e-mailen keresztüli egyszeri jelszót a többtényezős hitelesítéshez. Az MFA-beállítások függetlenek a feltételes hozzáférési beállításoktól. Az MFA-t úgy állíthatja be, hogy **mindig** be legyen állítva, hogy az MFA mindig kötelező legyen a feltételes hozzáférés beállítástól függetlenül. Az MFA beállítható **úgy is, hogy az** MFA csak akkor legyen kötelező, ha az aktív feltételes hozzáférési szabályzat megköveteli.

> [!IMPORTANT]
> Ha a feltételes hozzáférési szabályzata hozzáférést biztosít az MFA-hoz, de a felhasználó még nem regisztrált telefonszámot, előfordulhat, hogy a felhasználó le lesz tiltva.

::: zone pivot="b2c-user-flow"

Egy felhasználói folyamat feltételes hozzáférésének engedélyezéséhez győződjön meg arról, hogy a verzió támogatja a feltételes hozzáférést. A felhasználói folyamatok ezen verziói **ajánlott** címkével rendelkeznek.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

1. Az **Azure-szolgáltatások** területen válassza a **Azure ad B2C** lehetőséget. Vagy a keresőmező használatával megkeresheti és kiválaszthatja a **Azure ad B2C**.

1. A **házirendek** területen válassza a **felhasználói folyamatok** elemet. Ezután válassza ki a felhasználói folyamatot.

1. Válassza a **Tulajdonságok** lehetőséget, és győződjön meg arról, hogy a felhasználói folyamat támogatja a feltételes hozzáférést, ha a **feltételes hozzáférés** feliratú beállítást keresi.
 
   ![Az MFA és a feltételes hozzáférés konfigurálása a tulajdonságok között](media/conditional-access-user-flow/add-conditional-access.png)

1. A **multi-Factor Authentication** szakaszban válassza ki a kívánt **MFA-módszert**, majd az MFA- **kényszerítés** területen válassza a **feltételes (ajánlott)** lehetőséget.
 
1. A **feltételes hozzáférés** szakaszban jelölje be a **feltételes hozzáférési házirendek betartatása** jelölőnégyzetet.

1. Kattintson a **Mentés** gombra.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Feltételes hozzáférés hozzáadása a Szabályzathoz

1. Példa egy feltételes hozzáférési szabályzatra a [githubon](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access).
1. Minden fájlban cserélje le a karakterláncot a `yourtenant` Azure ad B2C bérlő nevére. Ha például a B2C-bérlő neve *contosob2c*, az összes példánya `yourtenant.onmicrosoft.com` lesz `contosob2c.onmicrosoft.com` .
1. Töltse fel a szabályzat fájljait.

## <a name="test-your-custom-policy"></a>Egyéni szabályzat tesztelése

1. Válassza ki a `B2C_1A_signup_signin_with_ca` vagy a `B2C_1A_signup_signin_with_ca_whatif` szabályzatot az Áttekintés oldal megnyitásához. Ezután válassza a **felhasználói folyamat futtatása** lehetőséget. Az **alkalmazás** alatt válassza a *webapp1* lehetőséget. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Másolja az URL-címet a **felhasználói folyamat végpontjának futtatása** alatt.

1. A kockázatos bejelentkezés szimulálása érdekében nyissa meg a [Tor böngészőt](https://www.torproject.org/download/) , és használja az előző lépésben másolt URL-címet a regisztrált alkalmazásba való bejelentkezéshez.

1. Adja meg a kért információkat a bejelentkezési lapon, majd próbálja meg bejelentkezni. A rendszer visszaküldi a tokent, `https://jwt.ms` és megjelenik Önnek. A dekódolású jwt.ms-tokenben látnia kell, hogy a bejelentkezés le lett tiltva.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a létrehozott felhasználói folyamatot az Áttekintés oldal megnyitásához, majd válassza a **felhasználói folyamat futtatása** lehetőséget. Az **alkalmazás** alatt válassza a *webapp1* lehetőséget. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .

1. Másolja az URL-címet a **felhasználói folyamat végpontjának futtatása** alatt.

1. A kockázatos bejelentkezés szimulálása érdekében nyissa meg a [Tor böngészőt](https://www.torproject.org/download/) , és használja az előző lépésben másolt URL-címet a regisztrált alkalmazásba való bejelentkezéshez.

1. Adja meg a kért információkat a bejelentkezési lapon, majd próbálja meg bejelentkezni. A rendszer visszaküldi a tokent, `https://jwt.ms` és megjelenik Önnek. A dekódolású jwt.ms-tokenben látnia kell, hogy a bejelentkezés le lett tiltva.

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>A feltételes hozzáférés eredményeinek áttekintése a naplózási jelentésben

Feltételes hozzáférési esemény eredményének áttekintése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

3. Az **Azure-szolgáltatások** területen válassza a **Azure ad B2C** lehetőséget. Vagy a keresőmező használatával megkeresheti és kiválaszthatja a **Azure ad B2C**.

4. A **tevékenységek** területen válassza a **naplók** lehetőséget.

5. A napló szűréséhez állítsa a **Kategória** beállítást a **B2C** értékre, és állítsa be a **tevékenység erőforrástípus** **IdentityProtection** értékre. Ezután válassza az **Alkalmaz** lehetőséget.

6. Tekintse át a naplózási tevékenységet az elmúlt hét napban. A következő típusú tevékenységek tartoznak ide:

   - **Feltételes hozzáférési szabályzatok kiértékelése**: Ez a naplózási naplóbejegyzés azt jelzi, hogy a rendszer feltételes hozzáférési értékelést hajtott végre egy hitelesítés során.
   - **Felhasználó szervizelése**: Ez a bejegyzés azt jelzi, hogy a végfelhasználó megkapta a feltételes hozzáférési szabályzatok támogatását vagy követelményeit, és ezt a tevékenységet jelentette a kockázati motornak, hogy enyhítse (csökkentse a felhasználó kockázatát).

7. Válassza ki a **feltételes hozzáférési szabályzat kiértékelése** naplóbejegyzés a listában a **tevékenység részletei:** napló lap, amely a naplózási napló azonosítóit jeleníti meg, valamint a **További részletek** szakaszban található információkat:

   - **ConditionalAccessResult**: a feltételes szabályzat kiértékeléséhez szükséges engedély.
   - **AppliedPolicies**: az összes olyan feltételes hozzáférési szabályzat listája, amelyben teljesülnek a feltételek, és a szabályzatok be vannak kapcsolva.
   - **ReportingPolicies**: azoknak a feltételes hozzáférési házirendeknek a listája, amelyek a csak jelentés módra lettek beállítva, és a feltételek teljesültek.

## <a name="next-steps"></a>Következő lépések

[Felhasználói felület testreszabása Azure AD B2C felhasználói folyamatban](customize-ui-with-html.md)
