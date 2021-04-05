---
title: Egyéni attribútumok definiálása a Azure Active Directory B2Cban | Microsoft Docs
description: Az alkalmazáshoz tartozó egyéni attribútumok meghatározása Azure Active Directory B2Cban az ügyfelek adatainak gyűjtéséhez.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 17c73257db371bbec0c72a23b1303847a8d14102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607917"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Egyéni attribútumok definiálása a Azure Active Directory B2Cban

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A [jogcímek hozzáadása és a felhasználói bevitel testreszabása Egyéni szabályzatok használatával](configure-user-input.md) című cikkből megtudhatja, hogyan használhatja a beépített [felhasználói profilok attribútumait](user-profile-attributes.md). Ebben a cikkben egy egyéni attribútumot engedélyez a Azure Active Directory B2C (Azure AD B2C) címtárban. Később az új attribútumot egyéni jogcímként használhatja [felhasználói folyamatokban](user-flow-overview.md) vagy [Egyéni szabályzatokban](custom-policy-get-started.md) .

Az Azure AD B2C-címtár [beépített attribútumkészletet](user-profile-attributes.md) tartalmaz. Azonban gyakran kell létrehoznia saját attribútumokat az adott forgatókönyv kezeléséhez, például a következő esetekben:

* Egy ügyfél felé irányuló alkalmazásnak meg kell őriznie egy **LoyaltyId** attribútumot.
* Az identitás-szolgáltató egyedi felhasználói azonosítóval rendelkezik, amelyet **uniqueUserGUID** kell megőrizni.
* Az egyéni felhasználói utazásnak meg kell őriznie a felhasználó ( **migrationStatus**) állapotát, hogy más logika is működjön.

A használati feltételek *kiterjesztésének tulajdonsága*, az *egyéni attribútum* és az *egyéni jogcím* a jelen cikk kontextusában ugyanezt a dolgot tekinti át. A név a környezettől, például az alkalmazástól, az objektumtól vagy a házirendtől függően változhat.

Azure AD B2C segítségével kiterjesztheti az egyes felhasználói fiókokban tárolt attribútumok készletét. Ezeket az attribútumokat a [Microsoft Graph API](microsoft-graph-operations.md)használatával is elolvashatja és elvégezheti.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>Egyéni attribútum létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Jelölje ki előfizetői adatait, majd válassza a **Könyvtárváltás** lehetőséget.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza ki a **felhasználói attribútumok** elemet, majd kattintson a **Hozzáadás** gombra.
1. Adja meg az egyéni attribútum **nevét** (például "ShoeSize").
1. Válassza ki az **adattípust**. Csak a **String**, a **Boolean** és az **int** érték érhető el.
1. Igény szerint megadhat egy **leírást** az információs célokra.
1. Kattintson a **Létrehozás** lehetőségre.

Az egyéni attribútum már elérhető a **felhasználói attribútumok** listájában és a felhasználói folyamatokban való használathoz. Egyéni attribútum csak akkor jön létre, amikor az első alkalommal használja a felhasználói folyamatokban, és nem, amikor hozzáadja a **felhasználói attribútumok** listájához.

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Egyéni attribútum használata a felhasználói folyamatokban

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki a szabályzatot (például "B2C_1_SignupSignin") a megnyitásához.
1. Válassza ki a **felhasználói attribútumok** elemet, majd válassza ki az egyéni attribútumot (például "ShoeSize"). Kattintson a **Mentés** gombra.
1. Válassza ki az **alkalmazás jogcímeit** , majd válassza ki az egyéni attribútumot.
1. Kattintson a **Mentés** gombra.

Miután létrehozott egy új felhasználót egy felhasználói folyamattal, amely az újonnan létrehozott egyéni attribútumot használja, az objektum [Microsoft Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer)kérdezhető le. Azt is megteheti, hogy a felhasználói folyamat [Futtatás felhasználói folyamat futtatása](./tutorial-create-user-flows.md) funkciójával ellenőrzi az ügyfél élményét. Ekkor látnia kell a **ShoeSize** a regisztrációs útvonalon összegyűjtött attribútumok listájában, és az alkalmazásnak visszaadott tokenben tekintheti meg.

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C Extensions-alkalmazás

A bővítmény attribútumai csak az Application objektumon regisztrálhatók, annak ellenére, hogy egy felhasználóhoz tartozó adatmennyiséget tartalmaznak. A bővítmény attribútum a nevű alkalmazáshoz van csatolva `b2c-extensions-app` . Ne módosítsa ezt az alkalmazást, mert az Azure AD B2C használja a felhasználói adattároláshoz. Az alkalmazás az Azure AD B2C, az alkalmazások regisztrációja alatt található. Az alkalmazás tulajdonságainak beolvasása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza **a minden alkalmazás** lehetőséget.
1. Válassza ki a `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` alkalmazást.
1. Másolja a következő azonosítókat a vágólapra, és mentse őket:
    * **Alkalmazásazonosító**. Példa: `11111111-1111-1111-1111-111111111111`.
    * **Objektumazonosító**. Példa: `22222222-2222-2222-2222-222222222222`.

::: zone pivot="b2c-custom-policy"

## <a name="modify-your-custom-policy"></a>Egyéni szabályzat módosítása

A házirendben szereplő egyéni attribútumok engedélyezéséhez adja meg az **alkalmazás azonosítóját** és az ALKALMAZÁSOBJEKTUM- **azonosítót** a AAD-Common technikai profil metaadataiban. A *HRE-Common* Technical profil az alapszintű [Azure Active Directory](active-directory-technical-profile.md) technikai profilban található, és támogatást nyújt az Azure ad-felhasználók felügyeletéhez. A többi Azure AD technikai profil tartalmazza a konfigurációjának kihasználása AAD-Common. Bírálja felül az AAD-Common technikai profilt a kiterjesztési fájlban.

1. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Keresse meg a ClaimsProviders elemet. Adjon hozzá egy új ClaimsProvider a ClaimsProviders elemhez.
1. Szúrja be a korábban rögzített **alkalmazás-azonosítót** a nyitó `<Item Key="ClientId">` és a záró `</Item>` elemek között.
1. Szúrja be a korábban rögzített **alkalmazás-ObjectId** a nyitó `<Item Key="ApplicationObjectId">` és a záró `</Item>` elemek között.

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="AAD-Common">
            <Metadata>
              <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
              <Item Key="ClientId"></Item>
              <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
              <Item Key="ApplicationObjectId"></Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles> 
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ```

## <a name="upload-your-custom-policy"></a>Egyéni szabályzat feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a Azure ad B2C bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
4. Válassza az **identitási élmény keretrendszert**.
5. Válassza az **egyéni házirend feltöltése** lehetőséget, majd töltse fel a módosított TrustFrameworkExtensions.xml házirend-fájlokat.

> [!NOTE]
> Amikor az Azure AD műszaki profilja megőrzi a jogcímet a címtárba, ellenőrzi, hogy létezik-e az egyéni attribútum. Ha nem, akkor az egyéni attribútumot hozza létre.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Egyéni attribútum létrehozása Azure Portal használatával

Ugyanazok a bővítmény-attribútumok vannak megosztva a beépített és az egyéni házirendek között. Ha egyéni attribútumokat ad hozzá a portál felületén keresztül, ezeket az attribútumokat az összes B2C-bérlőben található **B2C-Extensions-app** használatával regisztrálja a rendszer.

Ezeket az attribútumokat az egyéni szabályzatok használata előtt vagy után is létrehozhatja a portál felhasználói felületén. Amikor **loyaltyId** hoz létre a portálon, az alábbiak szerint kell megadnia:

|Name     |Használatban |
|---------|---------|
|`extension_loyaltyId`  | Egyéni szabályzat|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](microsoft-graph-operations.md)|

Az alábbi példa az egyéni attribútumok használatát mutatja be egy Azure AD B2C egyéni házirend-jogcím definíciójában.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

Az alábbi példa egy egyéni attribútum használatát mutatja be Azure AD B2C egyéni szabályzatban egy technikai profilban, bemenetben, kimenetben és megőrzött jogcímek között.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

::: zone-end

## <a name="using-custom-attribute-with-ms-graph-api"></a>Egyéni attribútum használata az MS Graph API

Microsoft Graph API támogatja a bővítmény-attribútumokkal rendelkező felhasználók létrehozását és frissítését. A Graph API a bővítmény attribútumait az egyezmény használatával nevezi el `extension_ApplicationClientID_attributename` , ahol az az `ApplicationClientID` alkalmazás **alkalmazás-(ügyfél-) azonosítója** `b2c-extensions-app` . Vegye figyelembe, hogy a bővítmény attribútumának nevében szereplő **alkalmazás-azonosító (ügyfél)** nem tartalmaz kötőjelet. Például:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyId": "212342" 
``` 

## <a name="next-steps"></a>Következő lépések

Kövesse a [jogcímek hozzáadását és a felhasználói bevitel egyéni házirendek használatával történő testreszabását](configure-user-input.md)ismertető útmutatót. Ez a példa egy beépített "City" jogcímet használ. Egyéni attribútum használatához cserélje le a "City" kulcsszót a saját egyéni attribútumaira.
