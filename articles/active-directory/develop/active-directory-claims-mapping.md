---
title: Azure AD-bérlői alkalmazás jogcímének testreszabása (PowerShell)
titleSuffix: Microsoft identity platform
description: Ez az oldal a Azure Active Directory leképezését ismerteti.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: e77155f8a6efd3916ae90fcb562d688bb5b5126f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598890"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>How to: Customize claimsmitted in tokens for a specific app in a tenant (Preview)

> [!NOTE]
> Ez a funkció felváltja és [](active-directory-saml-claims-customization.md) felülírja a portálon jelenleg kínált jogcímek testreszabását. Ha ugyanabban az alkalmazásban a dokumentumban részletezett Graph/PowerShell módszer mellett testreszabja a jogcímeket a portálon, az alkalmazáshoz kiadott jogkivonatok figyelmen kívül hagyják a portálon található konfigurációt. A dokumentumban részletezett módszerekkel készített konfigurációk nem jelennek meg a portálon.

> [!NOTE]
> Ez a képesség jelenleg nyilvános előzetes verzióban érhető el. Készüljön fel a módosítások visszavonására vagy eltávolítására. A funkció bármely azure Azure Active Directory (Azure AD-) előfizetésben elérhető a nyilvános előzetes verzióban. Ha azonban a funkció általánosan elérhetővé válik, a funkció bizonyos aspektusainak prémium szintű Azure AD-előfizetésre lehet szüksége. Ez a szolgáltatás támogatja a jogcím-leképezési szabályzatok konfigurálását WS-Fed, SAML, OAuth és OpenID Connect protokollokhoz.

A bérlői rendszergazdák ezzel a funkcióval szabhatják testre a bérlőjük adott alkalmazásához jogkivonatok által kibocsátott jogcímeket. A jogcím-leképezési szabályzatok a következőre használhatók:

- Válassza ki a jogkivonatok által tartalmazott jogcímeket.
- Hozzon létre még nem létező jogcímtípusokat.
- Válassza ki vagy módosítsa az adott jogcímek által kibocsátott adatok forrását.

Ebben a cikkben néhány olyan gyakori forgatókönyvet is végigveszünk, amelyek segítségével megértheti, hogyan használhatja a jogcím-leképezési [szabályzattípust.](reference-claims-mapping-policy-type.md)

Jogcím-leképezési szabályzat létrehozásakor jogcímet is kibocsáthat egy könyvtársémabővítmény attribútumból a jogkivonatok között. A bővítményattribútumhoz használja az *ExtensionID* azonosítót *az elemben* található azonosító `ClaimsSchema` helyett.  A bővítményattribútumokkal kapcsolatos további információkért lásd: [Using directory schema extension attributes (Könyvtársémák bővítményattribútumainak használata).](active-directory-schema-extensions.md)

## <a name="prerequisites"></a>Előfeltételek

A következő példákban szolgáltatásnév-szabályzatokat hozhat létre, frissíthet, csatolhet és törölhet. A jogcím-leképezési szabályzatok csak szolgáltatásnév-objektumokhoz rendelhetők hozzá. Ha még csak most ismerkedik az Azure AD-val, javasoljuk, hogy mielőtt folytatja ezeket a példákat, tájékozódjon az Azure AD-bérlőkről. [](quickstart-create-new-tenant.md)

Első lépésként tegye a következőket:

1. Töltse le az [Azure AD PowerShell-modul legújabb nyilvános előzetes kiadását.](https://www.powershellgallery.com/packages/AzureADPreview)
1. Futtassa a Connect parancsot az Azure AD rendszergazdai fiókjába való bejelentkezéshez. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenetet indít el.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. A szervezetben létrehozott összes szabályzatot az alábbi paranccsal láthatja. Javasoljuk, hogy a következő forgatókönyvek legtöbb művelete után futtassa ezt a parancsot annak ellenőrzéséhez, hogy a szabályzatok a várt módon vannak-e létrehozva.

   ``` powershell
   Get-AzureADPolicy
   ```

## <a name="omit-the-basic-claims-from-tokens"></a>Alapszintű jogcímek kihagyása a jogkivonatból

Ebben a példában egy olyan szabályzatot hoz létre, amely eltávolítja az alapszintű jogcímkészletet a csatolt szolgáltatásnévnek kiadott jogkivonatból. [](reference-claims-mapping-policy-type.md#claim-sets)

1. Hozzon létre egy jogcím-leképezési szabályzatot. Ez a szabályzat, amely adott szolgáltatásnévhez kapcsolódik, eltávolítja az alapszintű jogcímkészletet a jogkivonatból.
   1. A szabályzat létrehozásához futtassa a következő parancsot:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Az új szabályzatot a következő paranccsal láthatja, és lekérte a szabályzat ObjectId-ját:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Rendelje hozzá a szabályzatot a szolgáltatásnévhez. A szolgáltatásnév ObjectId-ját is be kell szereznie.
   1. A szervezet összes szolgáltatásának a lekérdezéséhez lekérdezheti a Microsoft Graph [API-t.](/graph/traverse-the-graph) Az [Explorerben Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)be az Azure AD-fiókjába.
   2. Ha a szolgáltatásnév ObjectId-ével van, futtassa a következő parancsot:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>Az EmployeeID és a TenantCountry jogcímekként való szerepeletése jogkivonatokként

Ebben a példában egy olyan szabályzatot hoz létre, amely hozzáadja az EmployeeID és a TenantCountry értéket a csatolt szolgáltatásnévhez kiadott jogkivonathoz. Az EmployeeID az SAML-jogkivonatok és a JWT-k név jogcímtípusaként lesz kiadva. A TenantCountry ország/régió jogcímtípusként van kiadva az SAML-jogkivonatok és a JWT-kben. Ebben a példában továbbra is a jogkivonatok alapszintű jogcímeit foglaljuk bele.

1. Hozzon létre egy jogcím-leképezési szabályzatot. Ez a szabályzat, amely adott szolgáltatásnévhez kapcsolódik, hozzáadja az EmployeeID és TenantCountry jogcímeket a jogkivonathoz.
   1. A szabályzat létrehozásához futtassa a következő parancsot:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Az új szabályzatot a következő paranccsal láthatja, és lekérte az ObjectId szabályzatot:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Rendelje hozzá a szabályzatot a szolgáltatásnévhez. A szolgáltatásnév ObjectId-ját is be kell szereznie.
   1. A szervezet összes szolgáltatásnévnek a lekérdezésével lekérdezheti a [Microsoft Graph API-t.](/graph/traverse-the-graph) Vagy az [Microsoft Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer)jelentkezzen be Azure AD-fiókjába.
   2. Ha a szolgáltatásnév ObjectId-ével van, futtassa a következő parancsot:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>Jogcím-átalakítás használata jogkivonatban

Ebben a példában egy olyan szabályzatot hoz létre, amely egy "JoinedData" egyéni jogcímet bocsát ki a csatolt szolgáltatásnévhez kiadott JWT-k számára. Ez a jogcím egy olyan értéket tartalmaz, amely a felhasználói objektum extensionattribute1 attribútumában tárolt adatok ".sandbox" értékkel való összevonásával jön létre. Ebben a példában kizárjuk a jogkivonatok alapszintű jogcímeit.

1. Hozzon létre egy jogcím-leképezési szabályzatot. Ez a szabályzat, amely adott szolgáltatásnévhez kapcsolódik, hozzáadja az EmployeeID és TenantCountry jogcímeket a jogkivonathoz.
   1. A szabályzat létrehozásához futtassa a következő parancsot:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Az új szabályzatot a következő paranccsal láthatja, és lekérte a szabályzat ObjectId-ját:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Rendelje hozzá a szabályzatot a szolgáltatásnévhez. A szolgáltatásnév ObjectId-ját is be kell szereznie.
   1. A szervezet összes szolgáltatásnévnek a lekérdezésével lekérdezheti [a Microsoft Graph API-t.](/graph/traverse-the-graph) Az [Explorerben Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)be az Azure AD-fiókjába.
   2. Ha a szolgáltatásnév ObjectId-ével van, futtassa a következő parancsot:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>Biztonsági szempontok

A jogkivonatokat megkapó alkalmazások arra a tényre támaszkodnak, hogy a jogcímértékeket az Azure AD állította ki mérvadó módon, és nem lehet illetéktelenül módosítva. Ha azonban jogcím-leképezési szabályzatokkal módosítja a jogkivonat tartalmát, előfordulhat, hogy ezek a feltételezések már nem helyesek. Az alkalmazásoknak explicit módon meg kell tudomásul venniük, hogy a jogkivonatokat a jogcím-leképezési szabályzat létrehozója módosította, hogy megvédjék magukat a kártékony felhasználók által létrehozott jogcím-leképezési szabályzatokkal szemben. Ez a következő módokon lehetséges:

- Egyéni aláírókulcs konfigurálása
- Frissítse az alkalmazásjegyzéket a leképezett jogcímek elfogadásához.
 
E nélkül az Azure AD egy hibakódot [ `AADSTS50146` ad vissza.](reference-aadsts-error-codes.md#aadsts-error-codes)

### <a name="custom-signing-key"></a>Egyéni aláírókulcs

Ha egyéni aláírókulcsot szeretne hozzáadni a szolgáltatásnév-objektumhoz, a Azure PowerShell-parancsmag használatával létrehozhat egy tanúsítványkulcs-hitelesítő adatokat az [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) Application objektumhoz.

A jogcím-leképezést engedélyező alkalmazásoknak érvényesíteniük kell a jogkivonat-aláíró kulcsukat úgy, hogy hozzáfűzik a OpenID Connect `appid={client_id}` [metaadat-kérelmekhez.](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) Az alábbiakban látható a OpenID Connect használt metaadat-dokumentum formátuma:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Az alkalmazásjegyzék frissítése

Másik lehetőségként beállíthatja a tulajdonságot a következőre `acceptMappedClaims` `true` az [alkalmazásjegyzékben:](reference-app-manifest.md). Az [apiApplication](/graph/api/resources/apiapplication#properties)erőforrástípussal dokumentált módon ez lehetővé teszi, hogy az alkalmazás egyéni aláírókulcs megadása nélkül használja a jogcím-leképezést.

Ehhez a kért jogkivonat célközönségének az Azure AD-bérlő ellenőrzött tartománynevét kell használnia, ami azt jelenti, hogy a (az alkalmazásjegyzékben a által képviselt) értéket kell beállítania, például vagy (egyszerűen az alapértelmezett bérlőnév `Application ID URI` `identifierUris` `https://contoso.com/my-api` `https://contoso.onmicrosoft.com/my-api` használatával).

Ha nem használ ellenőrzött tartományt, az Azure AD az `AADSTS501461` "AcceptMappedClaims csak az alkalmazás GUID azonosítóját vagy a bérlő ellenőrzött tartományaiban található célközönségnek megfelelő jogkivonatok célközönségét támogatja) hibaüzenetet fogja *visszaadni. Módosítsa az erőforrás-azonosítót, vagy használjon alkalmazásspecifikus aláírókulcsot."*

## <a name="next-steps"></a>Következő lépések

- További [információért olvassa el a jogcím-leképezési szabályzattípusra](reference-claims-mapping-policy-type.md) vonatkozó referenciát.
- Az SAML-jogkivonatban kiadott jogcímek testreszabásának Azure Portal: How to: Customize claims issued in the SAML token for enterprise applications (Útmutató: Az SAML-jogkivonatban kiállított jogcímek testreszabása vállalati [alkalmazásokhoz)](active-directory-saml-claims-customization.md)
- A bővítményattribútumokkal kapcsolatos további információkért lásd: [Using directory schema extension attributes in claims (Címtársémák bővítményattribútumainak használata jogcímek között).](active-directory-schema-extensions.md)
