---
title: Jogcímek REST API a B2C egyéni házirendben
titleSuffix: Azure AD B2C
description: Bevezetés a REST-szolgáltatásokkal kommunikáló Azure AD B2C felhasználói út létrehozásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc2b72779460c2b7e3999204ace50ca57388b9a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89594186"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>REST API jogcímek cseréjének integrálása a Azure AD B2C egyéni szabályzatba

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az identitási élmény keretrendszere, amely Azure Active Directory B2C (Azure AD B2C), a felhasználói úton lévő REST API-kkal is integrálható. Ebből a cikkből megtudhatja, hogyan hozhat létre olyan felhasználói utat, amely [Rest-technikai profil](restful-technical-profile.md)használatával kommunikál egy Rest-szolgáltatással.

A Azure AD B2C használatával saját üzleti logikát adhat hozzá egy felhasználói útra, ha meghívja saját REST-szolgáltatását. Az Identity Experience Framework az adatok küldését és fogadását is elküldheti a REST-szolgáltatástól az Exchange jogcímeihez. Megteheti például a következőt:

- **Felhasználói bemeneti adatok érvényesítése**. Ellenőrizheti például, hogy a felhasználó által megadott e-mail-cím szerepel-e az ügyfél adatbázisában, és ha nem, akkor hibát jelez.
- **Jogcímek feldolgozása**. Ha a felhasználó az első nevet adja meg az összes kisbetűs vagy az összes nagybetűvel, akkor a REST API csak az első betűvel formázhatja a nevet, és visszaküldheti a Azure AD B2Cnak.
- **A vállalati üzletági alkalmazások további integrálásával gazdagíthatja a felhasználói adatmennyiséget**. A REST-szolgáltatás képes fogadni a felhasználó e-mail-címét, lekérdezni az ügyfél adatbázisát, és visszaküldeni a felhasználó hűségi számát Azure AD B2C. Ezután a visszaküldési jogcímeket a felhasználó Azure AD-fiókjába, a következő hangolási lépésekben vagy a hozzáférési tokenbe foglalt módon lehet tárolni.
- **Egyéni üzleti logikát futtathat**. Leküldéses értesítések küldhetők, vállalati adatbázisok frissítése, felhasználói áttelepítési folyamat futtatása, engedélyek kezelése, naplózási adatbázisok és egyéb munkafolyamatok elvégzése.

![REST-alapú szolgáltatási jogcímek cseréjének ábrája](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

> [!NOTE]
> Ha a REST-szolgáltatás lassú vagy nem válaszol a Azure AD B2Cre, az időkorlát 30 másodperc, az újrapróbálkozások száma pedig 2 alkalommal (azaz 3 próbálkozás összesen). Az időkorlát és az újrapróbálkozások száma beállítás jelenleg nem konfigurálható.

## <a name="calling-a-restful-service"></a>REST-szolgáltatás hívása

Az interakció magában foglalja a jogcímek REST API jogcímek és Azure AD B2C közötti információcserét. A REST-szolgáltatásokkal való integrációt a következő módokon lehet megtervezni:

- **Ellenőrzési technikai profil**. A REST-szolgáltatás meghívása a megadott [önjelölt technikai profil](self-asserted-technical-profile.md) [érvényesítési technikai profiljába](validation-technical-profile.md) , vagy egy [megjelenítési vezérlőelem](display-controls.md)ellenőrző [megjelenítési vezérlőelemére](display-control-verification.md) vonatkozik. Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott, a felhasználói út továbbítása előtti lépéseket. Az érvényesítési technikai profillal a következőket teheti:

  - Jogcímek küldése a REST API.
  - Érvényesítse a jogcímeket, és dobja el a felhasználónak megjelenő egyéni hibaüzeneteket.
  - Visszaküldheti a jogcímeket a REST APIról a későbbi előkészítési lépésekbe.

- **Jogcím-Exchange**. A közvetlen jogcím-csere úgy konfigurálható, hogy egy REST API technikai profilt hív meg közvetlenül egy [felhasználói út](userjourneys.md)előkészítési lépése alapján. Ez a definíció a következőre korlátozódik:

  - Jogcímek küldése a REST API.
  - Érvényesítse a jogcímeket, és dobja el az alkalmazásnak visszaadott egyéni hibaüzeneteket.
  - Visszaküldheti a jogcímeket a REST APIról a későbbi előkészítési lépésekbe.

Az egyéni házirend által definiált felhasználói út bármely lépésében hozzáadhat egy REST API hívást. Meghívhat például egy REST API:

- Bejelentkezéskor közvetlenül a Azure AD B2C érvényesíti a hitelesítő adatokat.
- Közvetlenül a bejelentkezés után.
- Mielőtt Azure AD B2C új fiókot hoz létre a címtárban.
- Miután Azure AD B2C létrehoz egy új fiókot a címtárban.
- Mielőtt Azure AD B2C kiadja a hozzáférési jogkivonatot.

![Ellenőrzési technikai profil gyűjteménye](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Adatok küldése

A [Rest-technikai profilban](restful-technical-profile.md)a `InputClaims` elem tartalmazza a REST szolgáltatásnak küldendő jogcímek listáját. A jogcím nevét leképezheti a REST-szolgáltatásban definiált névre, beállíthatja az alapértelmezett értéket, és használhatja a [jogcímek feloldóit](claim-resolver-overview.md).

Beállíthatja, hogy a bemeneti jogcímek hogyan legyenek elküldve a REST-jogcím-szolgáltatónak a SendClaimsIn attribútum használatával. Lehetséges értékek:

- A **szövegtörzs** a http post kérelem törzsében JSON formátumban lett elküldve.
- Az **űrlap** a http post kérelem törzsében, a "&" tagolt kulcs értékének formátumában lett elküldve.
- A **fejléc** a HTTP Get kérelem fejlécében lett elküldve.
- A HTTP GET kérelem lekérdezési karakterláncában elküldve a **querystring**.

Ha a **törzs** beállítás be van állítva, a REST API technikai profil lehetővé teszi, hogy összetett JSON-adattartalmat küldjön egy végpontnak. További információ: JSON- [adattartalom küldése](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Adatfogadás

A `OutputClaims` [Rest-technikai profil](restful-technical-profile.md) eleme tartalmazza a REST API által visszaadott jogcímek listáját. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét a REST APIban definiált névre. Olyan jogcímeket is tartalmazhat, amelyeket nem ad vissza a REST API Identity Provider, ha beállítja a DefaultValue attribútumot.

A REST-jogcím-szolgáltató által elemzett kimeneti jogcímek mindig elvárják, hogy elemezzenek egy egyszerű JSON-törzsre adott választ, például:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

A kimeneti jogcímeknek a következőhöz hasonlóan kell kinézniük:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Beágyazott JSON-törzs válaszának elemzéséhez állítsa a ResolveJsonPathsInJsonTokens metaadatait igaz értékre. A kimeneti jogcímben állítsa be a PartnerClaimType a kimenetként használni kívánt JSON-útvonal elemre.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


A kimeneti jogcímeknek a következőhöz hasonlóan kell kinézniük:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Biztonsági szempontok

Biztosítania kell a REST API-végpontot, hogy csak a hitelesített ügyfelek kommunikáljanak velük. A REST APInak HTTPS-végpontot kell használnia. Állítsa be a AuthenticationType metaadatait az alábbi hitelesítési módszerek egyikére:

- Az **ügyféltanúsítvány** korlátozza a hozzáférést az ügyféltanúsítvány-alapú hitelesítés használatával. Csak a megfelelő tanúsítvánnyal rendelkező szolgáltatások férhetnek hozzá az API-hoz. Az ügyféltanúsítványt egy Azure AD B2C házirend kulcsában tárolja. További információ a [Rest szolgáltatás biztonságossá](secure-rest-api.md#https-client-certificate-authentication)tételéről az Ügyféltanúsítványok használatával.
- Az **alapszintű** biztonságossá teszi a REST API http alapszintű hitelesítéssel. Csak ellenőrzött felhasználók férhetnek hozzá az API-hoz, beleértve a Azure AD B2C is. A Felhasználónév és a jelszó Azure AD B2C szabályzat kulcsaiban tárolódik. Megtudhatja, hogyan [védheti meg Rest-szolgáltatásait http alapszintű hitelesítés használatával](secure-rest-api.md#http-basic-authentication).
- A **tulajdonos** korlátozza a hozzáférést az ügyfél OAuth2 hozzáférési jogkivonatának használatával. A hozzáférési token egy Azure AD B2C házirend-kulcsban van tárolva. További információ a [Rest szolgáltatás biztonságossá tételéről a tulajdonosi jogkivonat használatával](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>REST API platform
A REST API bármilyen platformon alapulhat, és bármilyen programozási nyelven írt, feltéve, hogy biztonságos, és a REST-alapú [műszaki profilban](restful-technical-profile.md)megadott jogcímeket képes elküldeni és fogadni.

## <a name="localize-the-rest-api"></a>A REST API honosítása
A REST technikai profilban érdemes lehet elküldeni az aktuális munkamenet nyelvét/területi beállítását, és ha szükséges, egy honosított hibaüzenetet kell felvennie. A [jogcímek feloldójának](claim-resolver-overview.md)használatával a kontextusbeli jogcímek, például a felhasználó nyelve is elküldhető. Az alábbi példa egy REST-alapú műszaki profilt mutat be, amely bemutatja ezt a forgatókönyvet.

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Hibaüzenetek kezelési üzenetei

Előfordulhat, hogy a REST API hibaüzenetet kell visszaadnia, például: "a felhasználó nem található a CRM rendszerben." Hiba esetén a REST API HTTP 409 hibaüzenetet ad vissza (ütközési válasz állapotkód). További információkért tekintse meg a [Rest műszaki profilt](restful-technical-profile.md#returning-validation-error-message).

Ez csak akkor érhető el, ha egy REST API technikai profilt hív meg egy érvényesítési technikai profilból. Ez lehetővé teszi a felhasználó számára, hogy kijavítsa az adatokat az oldalon, és a lap beküldése után ismét futtassa az ellenőrzést.

Egy HTTP 409-válasz szükséges ahhoz, hogy megakadályozza a további ellenőrzési technikai profilok feldolgozását ebben a koordinálási lépésben.

Ha közvetlenül egy felhasználói útra hivatkozik REST API műszaki profilra, a rendszer visszairányítja a felhasználót a függő entitás alkalmazására a vonatkozó hibaüzenettel.

## <a name="publishing-your-rest-api"></a>A REST API közzététele

A REST API szolgáltatásra irányuló kérés Azure AD B2C kiszolgálóról származik. A REST API szolgáltatást közzé kell tenni egy nyilvánosan elérhető HTTPS-végponton. Az REST API-hívások egy Azure-adatközpont IP-címéről érkeznek.

Tervezze meg REST API szolgáltatását és a mögöttes összetevőit (például az adatbázist és a fájlrendszert) a rendelkezésre álláshoz.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket a REST-technikai profil használatának példáit illetően:

- [Bemutató: REST API jogcím-cserék integrálása a Azure AD B2C felhasználói úton a felhasználói bevitel ellenőrzésekor](custom-policy-rest-api-claims-validation.md)
- [Forgatókönyv: REST API-jogcímek hozzáadása egyéni házirendekhez Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [A REST API szolgáltatásainak védelme](secure-rest-api.md)
- [Hivatkozás: REST-technikai profil](restful-technical-profile.md)
