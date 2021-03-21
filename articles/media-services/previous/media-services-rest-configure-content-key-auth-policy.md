---
title: Tartalmi kulcs engedélyezési házirendjének konfigurálása a REST-Azure-ban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy engedélyezési házirendet egy tartalmi kulcshoz a Media Services REST API használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7af5f9e2-8ed8-43f2-843b-580ce8759fd4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 66649eb6109339cd7723ab4a7e3a12c5c322973d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017290"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Dinamikus titkosítás: a tartalmi kulcs engedélyezési házirendjének konfigurálása

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Áttekintés
 A Azure Media Services használatával a tartalmat titkosíthatja (dinamikusan) a Advanced Encryption Standard (AES) használatával, 128 bites titkosítási kulcsokkal és PlayReady vagy Widevine digitális Rights managementtel (DRM). A Media Services egy olyan szolgáltatást is biztosít, amely kulcsok és PlayReady/Widevine-licencek kézbesítését teszi lehetővé a hitelesítő ügyfelek számára.

Ha Media Services szeretne titkosítani egy adategységet, hozzá kell rendelnie egy titkosítási kulcsot (CommonEncryption vagy EnvelopeEncryption) az eszközhöz. További információkért lásd: [tartalom-kulcsok létrehozása a REST használatával](media-services-rest-create-contentkey.md). Emellett konfigurálnia kell a kulcshoz tartozó engedélyezési házirendeket is (a jelen cikkben leírtak szerint).

Ha egy lejátszó egy adatfolyamot kér, Media Services a megadott kulccsal titkosítja a tartalmat AES vagy PlayReady titkosítás használatával. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. Annak megállapításához, hogy a felhasználó jogosult-e a kulcs lekérésére, a szolgáltatás kiértékeli a kulcshoz megadott engedélyezési házirendeket.

A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalmi kulcs engedélyezési házirendje egy vagy több engedélyezési korlátozást is tartalmazhat a nyitott vagy jogkivonat-korlátozás használatával. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. Media Services támogatja a tokeneket az egyszerű webes jogkivonat ([SWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)) és a JSON web token (JWT) formátumokban.

Media Services nem biztosít STS-t. Létrehozhat egy egyéni STS-t, vagy használhatja a Azure Active Directory (Azure AD) jogkivonatok kibocsátására. Az STS-t úgy kell konfigurálni, hogy a megadott kulccsal aláírt tokent hozzon létre, és kiadja a jogkivonat-korlátozási konfigurációban megadott jogcímeket (a jelen cikkben leírtak szerint). Ha a jogkivonat érvényes, és a jogkivonatban lévő jogcímek egyeznek a tartalmi kulcshoz konfigurált jogcímekkel, a Media Services Key Delivery Service visszaadja a titkosítási kulcsot az ügyfélnek.

További információért tekintse át a következő cikkeket:
- [JWT jogkivonat-hitelesítés](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Azure Media Services OWIN MVC-alapú alkalmazás integrálása Azure Active Directoryekkel és a JWT-jogcímek alapján történő kézbesítés korlátozása](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Néhány szempontot figyelembe kell venni
* A dinamikus csomagolás és a dinamikus titkosítás használatához győződjön meg arról, hogy az adatfolyam-végpont, amelyről a tartalmat továbbítani szeretné, "Running" állapotban kell lennie.
* Az eszköznek az adaptív sávszélességű MP4 vagy az adaptív sávszélességű Smooth Streaming fájlokat kell tartalmaznia. További információ: [eszköz kódolása](media-services-encode-asset.md).
* Töltse fel és kódolja az eszközeit az Assetcreationoptions alapján határozhatja. StorageEncrypted kapcsoló használatával.
* Ha azt tervezi, hogy több, azonos házirend-konfigurációt igénylő tartalmi kulcsra van szüksége, javasoljuk, hogy hozzon létre egy engedélyezési házirendet, és használja azt több tartalmi kulccsal.
* A Key Delivery szolgáltatás 15 percig gyorsítótárazza a ContentKeyAuthorizationPolicy és kapcsolódó objektumait (házirend-beállítások és korlátozások). Létrehozhat ContentKeyAuthorizationPolicy, és megadhatja a jogkivonat-korlátozást, tesztelheti, majd frissítheti a szabályzatot a nyitott korlátozással. Ez a folyamat nagyjából 15 percet vesz igénybe, mielőtt a házirend a házirend nyílt verziójára vált.
* Az objektumhoz tartozó továbbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell minden meglévő lokátort, majd létre kell hoznia egy újat.
* Jelenleg nem titkosíthatja a progresszív letöltéseket.
* A Media Services streaming Endpoint a CORS Access-Control-Allow-Origin fejléc értékét állítja be az elővizsgálati válaszban helyettesítő karakterként \* . Ez az érték jól működik a legtöbb játékossal, beleértve a Azure Media Player, a Roku és a JWPlayer és egyebeket. Egyes dash.jst használó játékosok azonban nem működnek, mivel a hitelesítő adatok "include" értékre van állítva, a dash.js nem engedélyezik a "" helyettesítő karaktert a \* hozzáférés-vezérlés – engedélyezés – forrás értékeként. Ha az ügyfelet egyetlen tartományból futtatja, megkerülő megoldásként dash.js a korlátozást, Media Services az elővizsgálati válasz fejlécében megadhatja az adott tartományt. Segítségért nyisson meg egy támogatási jegyet a Azure Portalon keresztül.

## <a name="aes-128-dynamic-encryption"></a>AES-128 dinamikus titkosítás
> [!NOTE]
> A Media Services REST API használatakor a következő szempontokat kell figyelembe venni.
> 
> Ha a Media Servicesban lévő entitásokhoz fér hozzá, a HTTP-kérelmekben meg kell adnia egy adott fejléc-mezőt és-értéket. További információ: [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md).
> 
> 
> 

### <a name="open-restriction"></a>Nyitott korlátozás
A megnyitási korlátozás azt jelenti, hogy a rendszer minden olyan felhasználó számára elérhetővé teszi a kulcsot, aki a kulcsot kéri. Ez a korlátozás tesztelési célokra is hasznos lehet.

Az alábbi példa egy nyílt engedélyezési házirendet hoz létre, és hozzáadja azt a tartalmi kulcshoz.

#### <a name="create-contentkeyauthorizationpolicies"></a><a id="ContentKeyAuthorizationPolicies"></a>ContentKeyAuthorizationPolicies létrehozása
Kérés:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 36

{"Name":"Open Authorization Policy"}
```

Válasz:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 211
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Adb4593da-f4d1-4cc5-a92a-d20eacbabee4')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
request-id: aabfa731-e884-4bf3-8314-492b04747ac4
x-ms-request-id: aabfa731-e884-4bf3-8314-492b04747ac4
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 08:25:56 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:db4593da-f4d1-4cc5-a92a-d20eacbabee4","Name":"Open Authorization Policy"}
```

#### <a name="create-contentkeyauthorizationpolicyoptions"></a><a id="ContentKeyAuthorizationPolicyOptions"></a>ContentKeyAuthorizationPolicyOptions létrehozása
Kérés:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 168

{"Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}
```

Válasz:    

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 349
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
x-ms-request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 08:56:40 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:57829b17-1101-4797-919b-f816f4a007b7","Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a><a id="LinkContentKeyAuthorizationPoliciesWithOptions"></a>ContentKeyAuthorizationPolicies csatolása lehetőségekkel
Kérés:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3A0baa438b-8ac2-4c40-a53c-4d4722b78715')/$links/Options HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 9847f705-f2ca-4e95-a478-8f823dbbaa29
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 154

{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')"}
```

Válasz:

```output
HTTP/1.1 204 No Content
```

#### <a name="add-an-authorization-policy-to-the-content-key"></a><a id="AddAuthorizationPolicyToKey"></a>Engedélyezési szabályzat hozzáadása a tartalmi kulcshoz
Kérés:

```console
PUT https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A2e6d36a7-a17c-4e9a-830d-eca23ad1a6f9') HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: e613efff-cb6a-41b4-984a-f4f8fb6e76a4
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 78

{"AuthorizationPolicyId":"nb:ckpid:UUID:c06cebb8-c4f0-4d1a-ba00-3273fb2bc3ad"}
```

Válasz:

```output
HTTP/1.1 204 No Content
```

### <a name="token-restriction"></a>Jogkivonat-korlátozás
Ez a szakasz azt ismerteti, hogyan hozható létre a tartalmi kulcs engedélyezési szabályzata, és hogyan rendelhető hozzá a tartalomhoz. Az engedélyezési házirend leírja, hogy milyen engedélyezési követelmények teljesülése szükséges annak megállapításához, hogy a felhasználó jogosult-e a kulcs fogadására. A hitelesítő kulcsok listája például tartalmazza azt a kulcsot, amelyet a jogkivonat írt alá?

A jogkivonat-korlátozási beállítás konfigurálásához XML-t kell használnia a jogkivonat engedélyezési követelményeinek leírásához. A jogkivonat-korlátozás konfigurációs XML-fájljának meg kell felelnie a következő XML-sémának:


#### <a name="token-restriction-schema"></a><a id="schema"></a>Jogkivonat-korlátozási séma
```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
  <xs:complexType name="TokenClaim">
    <xs:sequence>
      <xs:element name="ClaimType" nillable="true" type="xs:string" />
      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
  <xs:complexType name="TokenRestrictionTemplate">
    <xs:sequence>
      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
  <xs:complexType name="ArrayOfTokenVerificationKey">
    <xs:sequence>
      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
  <xs:complexType name="TokenVerificationKey">
    <xs:sequence />
  </xs:complexType>
  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
  <xs:complexType name="ArrayOfTokenClaim">
    <xs:sequence>
      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
  <xs:complexType name="SymmetricVerificationKey">
    <xs:complexContent mixed="false">
      <xs:extension base="tns:TokenVerificationKey">
        <xs:sequence>
          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
</xs:schema>
```

Ha a jogkivonat-korlátozott szabályzatot konfigurálja, meg kell adnia az elsődleges ellenőrző kulcsot, a kiállítót és a célközönség paramétereit. Az elsődleges ellenőrző kulcs tartalmazza azt a kulcsot, amelyet a jogkivonat aláírt. A kibocsátó a jogkivonatot kiállító STS. A célközönség (más néven hatókör) leírja a jogkivonat célját vagy azt az erőforrást, amelyet a jogkivonat engedélyez a hozzáféréshez. A Media Services Key Delivery Service ellenőrzi, hogy a jogkivonat értékei egyeznek-e a sablon értékeivel.

Az alábbi példa jogkivonat-korlátozást tartalmazó engedélyezési házirendet hoz létre. Ebben a példában az ügyfélnek olyan jogkivonatot kell bemutatnia, amely tartalmazza az aláíró kulcsot (VerificationKey), egy jogkivonat-kiállítót és a szükséges jogcímeket.

### <a name="create-contentkeyauthorizationpolicies"></a>ContentKeyAuthorizationPolicies létrehozása
Hozzon létre egy jogkivonat-korlátozási szabályzatot, ahogy az a "[create ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies)" című szakaszban látható.

### <a name="create-contentkeyauthorizationpolicyoptions"></a>ContentKeyAuthorizationPolicyOptions létrehozása
Kérés:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 1079

{"Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

Válasz:    

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1260
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae1ef6145-46e8-4ee6-9756-b1cf96328c23')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
x-ms-request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:10:37 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e1ef6145-46e8-4ee6-9756-b1cf96328c23","Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>ContentKeyAuthorizationPolicies csatolása lehetőségekkel
Csatolja a ContentKeyAuthorizationPolicies a beállításokkal, ahogy az "[ContentKeyAuthorizationPolicies létrehozása](#ContentKeyAuthorizationPolicies)" című szakaszban is látható.

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Engedélyezési szabályzat hozzáadása a tartalmi kulcshoz
Adja hozzá a AuthorizationPolicy a ContentKey-hez, ahogy az "[engedélyezési házirend hozzáadása a tartalmi kulcshoz](#AddAuthorizationPolicyToKey)" című szakaszban látható.

## <a name="playready-dynamic-encryption"></a>PlayReady dinamikus titkosítás
A Media Services használatával konfigurálhatja azokat a jogokat és korlátozásokat, amelyeket a PlayReady DRM-futtatókörnyezetnek kényszeríteni kell, amikor egy felhasználó megpróbál lejátszani egy védett tartalmat. 

Ha a tartalmat a PlayReady-mel védik, az engedélyezési házirendben megadott egyik dolog egy XML-karakterlánc, amely meghatározza a [PlayReady-licenc sablonját](media-services-playready-license-template-overview.md). 

### <a name="open-restriction"></a>Nyitott korlátozás
A megnyitási korlátozás azt jelenti, hogy a rendszer minden olyan felhasználó számára elérhetővé teszi a kulcsot, aki a kulcsot kéri. Ez a korlátozás tesztelési célokra is hasznos lehet.

Az alábbi példa egy nyílt engedélyezési házirendet hoz létre, és hozzáadja azt a tartalmi kulcshoz.

#### <a name="create-contentkeyauthorizationpolicies"></a><a id="ContentKeyAuthorizationPolicies2"></a>ContentKeyAuthorizationPolicies létrehozása
Kérés:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 58

{"Name":"Deliver Common Content Key"}
```

Válasz:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 233
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Acc3c64a8-e2fc-4e09-bf60-ac954251a387')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
x-ms-request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:26:00 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:cc3c64a8-e2fc-4e09-bf60-ac954251a387","Name":"Deliver Common Content Key"}
```


#### <a name="create-contentkeyauthorizationpolicyoptions"></a>ContentKeyAuthorizationPolicyOptions létrehozása
Kérés:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 593

{"Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}
```

Válasz:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 774
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A1052308c-4df7-4fdb-8d21-4d2141fc2be0')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
x-ms-request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:23:24 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:1052308c-4df7-4fdb-8d21-4d2141fc2be0","Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>ContentKeyAuthorizationPolicies csatolása lehetőségekkel
Csatolja a ContentKeyAuthorizationPolicies a beállításokkal, ahogy az "[ContentKeyAuthorizationPolicies létrehozása](#ContentKeyAuthorizationPolicies)" című szakaszban is látható.

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Engedélyezési szabályzat hozzáadása a tartalmi kulcshoz
Adja hozzá a AuthorizationPolicy a ContentKey-hez, ahogy az "[engedélyezési házirend hozzáadása a tartalmi kulcshoz](#AddAuthorizationPolicyToKey)" című szakaszban látható.

### <a name="token-restriction"></a>Jogkivonat-korlátozás
A jogkivonat-korlátozási beállítás konfigurálásához XML-t kell használnia a jogkivonat engedélyezési követelményeinek leírásához. A jogkivonat-korlátozás konfigurációs XML-fájljának meg kell felelnie a "[jogkivonat-korlátozási séma](#schema)" szakaszban látható XML-sémának.

#### <a name="create-contentkeyauthorizationpolicies"></a>ContentKeyAuthorizationPolicies létrehozása
Hozzon létre ContentKeyAuthorizationPolicies a "[ContentKeyAuthorizationPolicies létrehozása](#ContentKeyAuthorizationPolicies2)" című szakaszban látható módon.

#### <a name="create-contentkeyauthorizationpolicyoptions"></a>ContentKeyAuthorizationPolicyOptions létrehozása
Kérés:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 1525

{"Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

Válasz:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1706
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae42bbeae-de42-4077-90e9-a844f297ef70')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
request-id: ccf8a4ba-731e-4124-8192-079592c251cc
x-ms-request-id: ccf8a4ba-731e-4124-8192-079592c251cc
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:58:47 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e42bbeae-de42-4077-90e9-a844f297ef70","Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>ContentKeyAuthorizationPolicies csatolása lehetőségekkel
Csatolja a ContentKeyAuthorizationPolicies a beállításokkal, ahogy az "[ContentKeyAuthorizationPolicies létrehozása](#ContentKeyAuthorizationPolicies)" című szakaszban is látható.

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Engedélyezési szabályzat hozzáadása a tartalmi kulcshoz
Adja hozzá a AuthorizationPolicy a ContentKey-hez, ahogy az "[engedélyezési házirend hozzáadása a tartalmi kulcshoz](#AddAuthorizationPolicyToKey)" című szakaszban látható.

## <a name="types-used-when-you-define-contentkeyauthorizationpolicy"></a><a id="types"></a>A ContentKeyAuthorizationPolicy definiálásához használt típusok
### <a name="contentkeyrestrictiontype"></a><a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
```csharp
public enum ContentKeyRestrictionType
{
    Open = 0,
    TokenRestricted = 1, 
    IPRestricted = 2, // IP restriction on content key is not currently supported, reserved for future.
}
```


> [!NOTE]
> A tartalmi kulcs engedélyezési házirendjeinek IP-korlátozása még nem érhető el a szolgáltatásban.


### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
```csharp
public enum ContentKeyDeliveryType
{
    None = 0,
    PlayReadyLicense = 1,
    BaselineHttp = 2,
    Widevine = 3
}
```

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések
Most, hogy beállította a tartalmi kulcs engedélyezési házirendjét, tekintse meg az [eszköz kézbesítési házirendjének konfigurálása](media-services-rest-configure-asset-delivery-policy.md)című témakört.
