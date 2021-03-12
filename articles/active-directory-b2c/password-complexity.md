---
title: Jelszó-összetettségi követelmények konfigurálása
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C felhasználók által szolgáltatott jelszavak bonyolultsági követelményeinek konfigurálása.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/12/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 81c6e58e34f30d5736c40c77a308321dee28ae34
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224265"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>A Azure Active Directory B2Cban található jelszavak bonyolultsági követelményeinek konfigurálása

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A Azure Active Directory B2C (Azure AD B2C) a fiók létrehozásakor támogatja a végfelhasználók által megadott jelszavak összetettségi követelményeinek módosítását. Alapértelmezés szerint a Azure AD B2C **erős** jelszavakat használ. A Azure AD B2C az ügyfelek által használható jelszavak bonyolultságának szabályozásához is támogatja a konfigurációs beállításokat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>Jelszavas szabály kényszerítése

A regisztráció vagy a jelszó alaphelyzetbe állítása során a végfelhasználónak meg kell adnia egy jelszót, amely megfelel az összetettségi szabályoknak. A jelszó bonyolultsági szabályait felhasználónként kell érvényesíteni. Egy felhasználói folyamatnak egy négyjegyű PIN-kódot kell megadnia a regisztráció során, míg egy másik felhasználói folyamat 8 karakterből álló karakterláncot igényel a regisztráció során. Használhat például egy olyan felhasználói folyamatot, amely különböző jelszó-bonyolultságot biztosít a felnőttek számára, mint a gyermekek számára.

A bejelentkezés során a rendszer soha nem kényszeríti ki a jelszó bonyolultságát. A rendszer a bejelentkezés során soha nem kéri a felhasználókat a jelszavuk módosítására, mert nem felel meg a jelenlegi összetettségi követelményeknek.

A jelszó bonyolultságát a következő típusú felhasználói folyamatokban lehet konfigurálni:

- Regisztrációs vagy bejelentkezési felhasználói folyamat
- Felhasználói folyamat jelszavának alaphelyzetbe állítása

Ha egyéni házirendeket használ, akkor (a[jelszó bonyolultságát egyéni szabályzatban állíthatja be](password-complexity.md)).

## <a name="configure-password-complexity"></a>Jelszó bonyolultságának konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
3. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
4. Válassza a **felhasználói folyamatok** lehetőséget.
2. Válasszon ki egy felhasználói folyamatot, majd kattintson a **Tulajdonságok** elemre.
3. A **jelszó bonyolultsága** területen módosítsa a felhasználói folyamat jelszavának bonyolultságát **egyszerű**, **erős** vagy **Egyéni** értékre.

### <a name="comparison-chart"></a>Összehasonlító diagram

| Összetettség | Leírás |
| --- | --- |
| Egyszerű | Egy legalább 8 – 64 karakterből álló jelszó. |
| Erős | Egy legalább 8 – 64 karakterből álló jelszó. Ehhez 3 kisbetűs, nagybetűs, szám vagy szimbólum közül 3. |
| Egyéni | Ez a beállítás biztosítja a jelszó bonyolultságára vonatkozó szabályok szabályozását.  Lehetővé teszi az egyéni hossz konfigurálását.  Emellett lehetővé teszi a csak számú jelszó (PIN-kód) fogadását. |

## <a name="custom-options"></a>Egyéni beállítások

### <a name="character-set"></a>Karakterkészlet

Lehetővé teszi, hogy csak számjegyeket (PIN-ket) vagy a teljes karakterkészletet fogadja el.

- A **számok csak** számjegyeket (0-9) engedélyeznek a jelszó beírása közben.
- **Minden** betűt, számot vagy szimbólumot engedélyez.

### <a name="length"></a>Hossz

Lehetővé teszi a jelszó hosszára vonatkozó követelmények szabályozását.

- A **minimális hossznak** legalább 4-nek kell lennie.
- A **maximális hossznak** nagyobbnak vagy egyenlőnek kell lennie a minimális hosszsal, és legfeljebb 256 karakter hosszú lehet.

### <a name="character-classes"></a>Karakter osztályok

Lehetővé teszi a jelszóban használt különböző karakterkészletek szabályozását.

- **2/4: kisbetűs karakter, nagybetűs karakter, szám (0-9), a szimbólum biztosítja,** hogy a jelszó legalább két karaktert tartalmazzon. Például egy szám és egy kisbetűs karakter.
- **3 – 4: kisbetűs karakter, nagybetűs karakter, szám (0-9), a szimbólum biztosítja,** hogy a jelszó legalább három karaktert tartalmazzon. Például egy szám, egy kisbetűs karakter és egy nagybetűs karakter.
- **4/4: kisbetűs karakter, nagybetűs karakter, szám (0-9), a szimbólum biztosítja,** hogy a jelszó tartalmazza az összes karaktert.

    > [!NOTE]
    > A **4 – 4** . megkövetelése végfelhasználói frusztrációt eredményezhet. Egyes tanulmányok azt mutatták, hogy ez a követelmény nem javítja a jelszó entrópiat. Lásd: a [NIST-jelszóra vonatkozó irányelvek](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>Jelszó-predikátum ellenőrzése

A jelszó bonyolultságának konfigurálásához bírálja felül a `newPassword` és a `reenterPassword` [jogcím típusait](claimsschema.md) a [predikátumok érvényességére](predicates.md#predicatevalidations)mutató hivatkozással. A PredicateValidations elem olyan predikátumok halmazát csoportosítja, amelyekkel egy felhasználói bemeneti ellenőrzés alkotható, amely alkalmazható a jogcím típusára. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a `newPassword` és a `reenterPassword` jogcímeket a **ClaimsSchema** elemhez.

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="newPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
        <ClaimType Id="reenterPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. A [predikátumok](predicates.md) alapszintű ellenőrzést határoznak meg a jogcím típusának ellenőrzéséhez, és igaz vagy hamis értéket ad vissza. Az érvényesítés egy megadott metódus-elem és a metódushoz tartozó paraméterek készletének használatával történik. Adja hozzá a következő predikátumokat a **BuildingBlocks** elemhez közvetlenül az elem bezárása után `</ClaimsSchema>` :

    ```xml
    <!-- 
    <BuildingBlocks>-->
      <Predicates>
        <Predicate Id="LengthRange" Method="IsLengthRange">
          <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
          <Parameters>
            <Parameter Id="Minimum">6</Parameter>
            <Parameter Id="Maximum">64</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Lowercase" Method="IncludesCharacters">
          <UserHelpText>a lowercase letter</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">a-z</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Uppercase" Method="IncludesCharacters">
          <UserHelpText>an uppercase letter</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">A-Z</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Number" Method="IncludesCharacters">
          <UserHelpText>a digit</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">0-9</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Symbol" Method="IncludesCharacters">
          <UserHelpText>a symbol</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
          </Parameters>
        </Predicate>
      </Predicates>
    <!-- 
    </BuildingBlocks>-->
    ```

1. Adja hozzá a következő predikátum-érvényesítéseket a **BuildingBlocks** elemhez közvetlenül az elem bezárása után `</Predicates>` :

    ```xml
    <!-- 
    <BuildingBlocks>-->
      <PredicateValidations>
        <PredicateValidation Id="CustomPassword">
          <PredicateGroups>
            <PredicateGroup Id="LengthGroup">
              <PredicateReferences MatchAtLeast="1">
                <PredicateReference Id="LengthRange" />
              </PredicateReferences>
            </PredicateGroup>
            <PredicateGroup Id="CharacterClasses">
              <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
              <PredicateReferences MatchAtLeast="3">
                <PredicateReference Id="Lowercase" />
                <PredicateReference Id="Uppercase" />
                <PredicateReference Id="Number" />
                <PredicateReference Id="Symbol" />
              </PredicateReferences>
            </PredicateGroup>
          </PredicateGroups>
        </PredicateValidation>
      </PredicateValidations>
    <!-- 
    </BuildingBlocks>-->
    ```

## <a name="disable-strong-password"></a>Erős jelszó letiltása 

A következő műszaki profilok [Active Directory műszaki profilok](active-directory-technical-profile.md), amelyek az Azure Active Directoryba való adatolvasást és-írást írják le. Bírálja felül ezeket a technikai profilokat a kiterjesztési fájlban. Ezzel `PersistedClaims` a beállítással letilthatja az erős jelszavas házirendet. Keresse meg a **ClaimsProviders** elemet.  Adja hozzá a következő jogcím-szolgáltatókat az alábbiak szerint:

```xml
<!-- 
<ClaimsProviders>-->
  <ClaimsProvider>
    <DisplayName>Azure Active Directory</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
        <PersistedClaims>
          <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </PersistedClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
        <PersistedClaims>
          <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </PersistedClaims>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders>-->
```

Ha a [Felhasználónév alapú bejelentkezési](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin) szabályzatot használja, frissítse a, a `AAD-UserWriteUsingLogonEmail` `AAD-UserWritePasswordUsingObjectId` és `LocalAccountWritePasswordUsingObjectId` a technikai profilokat a *DisableStrongPassword* házirenddel.

Mentse a házirend-fájlt.

## <a name="test-your-policy"></a>A szabályzat tesztelése

### <a name="upload-the-files"></a>A fájlok feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **identitási élmény keretrendszert**.
5. Az egyéni házirendek lapon kattintson a **házirend feltöltése** elemre.
6. Ha létezik, válassza a **házirend felülírása** lehetőséget, majd keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
7. Kattintson a **Feltöltés** gombra.

### <a name="run-the-policy"></a>A házirend futtatása

1. Nyissa meg a regisztrálási vagy bejelentkezési szabályzatot. Például *B2C_1A_signup_signin*.
2. **Alkalmazás** esetén válassza ki a korábban regisztrált alkalmazást. A token megjelenítéséhez a **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
3. Kattintson a **Futtatás most** parancsra.
4. Válassza a **regisztráció most** lehetőséget, adjon meg egy e-mail-címet, és adjon meg egy új jelszót. Útmutatást a jelszóra vonatkozó korlátozásokban talál. Fejezze be a felhasználói adatok beírását, majd kattintson a **Létrehozás** gombra. Ekkor meg kell jelennie a visszaadott token tartalmának.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [konfigurálhatja a jelszó módosítását a Azure Active Directory B2Cban](add-password-change-policy.md).
- További információ a [predikátumok](predicates.md) és a [PredicateValidations](predicates.md#predicatevalidations) elemeiről a IEF-hivatkozásban.


::: zone-end
