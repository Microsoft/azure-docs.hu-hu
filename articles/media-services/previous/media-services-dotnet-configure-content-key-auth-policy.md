---
title: A tartalmi kulcs engedélyezési házirendjének konfigurálása a Media Services .NET SDK használatával | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy engedélyezési házirendet egy tartalmi kulcshoz a Media Services .NET SDK használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 63cc20a9d7f6878961519aa28ae6478d9145852d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014213"
---
# <a name="configure-a-content-key-authorization-policy-by-using-the-media-services-net-sdk"></a>A tartalmi kulcs engedélyezési házirendjének konfigurálása a Media Services .NET SDK használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Áttekintés
 A Azure Media Services használatával a 128 bites titkosítási kulcsokkal vagy a [PlayReady digitális Rights Management (DRM)](https://www.microsoft.com/playready/overview/)használatával biztosíthatja a Advanced Encryption Standard (AES) védelemmel ellátott MPEG-DASH, Smooth Streaming és http Live Streaming (HLS) adatfolyamok továbbítását. A Media Services segítségével a Widevine DRM-mel titkosított DASH-streameket is továbbíthat. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik.

A Media Services egy kulcs/licenc kézbesítési szolgáltatást is biztosít, amelyből az ügyfelek AES-kulcsokat vagy PlayReady-vagy Widevine-licenceket szerezhetnek be a titkosított tartalom lejátszásához.

Ha Media Services szeretne titkosítani egy adategységet, hozzá kell rendelnie egy titkosítási kulcsot (CommonEncryption vagy EnvelopeEncryption) az eszközhöz. További információ: [Tartalomkulcsok létrehozása a .net](media-services-dotnet-create-contentkey.md)-tel. Emellett konfigurálnia kell a kulcshoz tartozó engedélyezési házirendeket is (a jelen cikkben leírtak szerint).

Ha egy lejátszó egy adatfolyamot kér, Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat AES vagy DRM titkosítás használatával. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. Annak megállapításához, hogy a felhasználó jogosult-e a kulcs lekérésére, a szolgáltatás kiértékeli a kulcshoz megadott engedélyezési házirendeket.

A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalmi kulcs engedélyezési házirendje egy vagy több engedélyezési korlátozást tartalmazhat. A beállítások nyitott vagy jogkivonat-korlátozás. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. Media Services támogatja a tokeneket az egyszerű webes jogkivonat ([SWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)) és a JSON web token ([JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3)) formátumban.

Media Services nem biztosít STS-t. Létrehozhat egy egyéni STS-t, vagy használhatja az Azure Access Control Service a jogkivonatok kibocsátására. Az STS-t úgy kell konfigurálni, hogy a megadott kulccsal aláírt tokent hozzon létre, és kiadja a jogkivonat-korlátozási konfigurációban megadott jogcímeket (a jelen cikkben leírtak szerint). Ha a jogkivonat érvényes, és a jogkivonatban lévő jogcímek egyeznek a tartalmi kulcshoz konfigurált jogcímekkel, a Media Services Key Delivery Service visszaadja a titkosítási kulcsot az ügyfélnek.

További információért tekintse át a következő cikkeket:

- [JWT jogkivonat-hitelesítés](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Azure Media Services OWIN MVC-alapú alkalmazás integrálása Azure Active Directory és a JWT-jogcímek alapján történő kézbesítés korlátozása](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Néhány szempontot figyelembe kell venni
* A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva állapotban. A tartalom folyamatos átvitelének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a folyamatos átviteli végpontnak "Running" állapotban kell lennie. 
* Az eszköznek az adaptív sávszélességű MP4 vagy az adaptív sávszélességű Smooth Streaming fájlokat kell tartalmaznia. További információ: [eszköz kódolása](media-services-encode-asset.md).
* Töltse fel és kódolja az eszközeit az Assetcreationoptions alapján határozhatja. StorageEncrypted kapcsoló használatával.
* Ha azt tervezi, hogy több, azonos házirend-konfigurációt igénylő tartalmi kulcsra van szüksége, javasoljuk, hogy hozzon létre egy engedélyezési házirendet, és használja azt több tartalmi kulccsal.
* A Key Delivery szolgáltatás 15 percig gyorsítótárazza a ContentKeyAuthorizationPolicy és kapcsolódó objektumait (házirend-beállítások és korlátozások). Létrehozhat ContentKeyAuthorizationPolicy, és megadhatja a jogkivonat-korlátozást, tesztelheti, majd frissítheti a szabályzatot a nyitott korlátozással. Ez a folyamat nagyjából 15 percet vesz igénybe, mielőtt a házirend a házirend nyílt verziójára vált.
* Az objektumhoz tartozó továbbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell minden meglévő lokátort, majd létre kell hoznia egy újat.
* Jelenleg nem titkosíthatja a progresszív letöltéseket.
* A Media Services streaming-végpontok az elővizsgálati válaszban a CORS "hozzáférés-vezérlés-engedélyezés-eredet" fejlécének értékét állítja be a következő helyettesítő karakterként: " \* ". Ez az érték jól működik a legtöbb játékossal, beleértve a Azure Media Player, a Roku és a JWPlayer és egyebeket. Az dashjs-t használó játékosok azonban nem működnek, mivel a hitelesítő adatok mód "include" értékre van állítva, az XMLHttpRequest a dashjs nem engedélyezi a " \* " hozzáférés-vezérlés-engedélyezés-forrás "karaktert. Ha az ügyfelet egyetlen tartományból futtatja, megkerülő megoldásként ezt a korlátozást dashjs, Media Services az elővizsgálati válasz fejlécében megadhatja az adott tartományt. Segítségért nyisson meg egy támogatási jegyet a Azure Portalon keresztül.

## <a name="aes-128-dynamic-encryption"></a>AES-128 dinamikus titkosítás
### <a name="open-restriction"></a>Nyitott korlátozás
A megnyitási korlátozás azt jelenti, hogy a rendszer minden olyan felhasználó számára elérhetővé teszi a kulcsot, aki a kulcsot kéri. Ez a korlátozás tesztelési célokra is hasznos lehet.

Az alábbi példa egy nyílt engedélyezési házirendet hoz létre, és hozzáadja azt a tartalmi kulcshoz:
```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>Jogkivonat-korlátozás
Ez a szakasz azt ismerteti, hogyan hozható létre a tartalmi kulcs engedélyezési szabályzata, és hogyan rendelhető hozzá a tartalomhoz. Az engedélyezési házirend leírja, hogy milyen engedélyezési követelmények teljesülése szükséges annak megállapításához, hogy a felhasználó jogosult-e a kulcs fogadására. A hitelesítő kulcsok listája például tartalmazza azt a kulcsot, amelyet a jogkivonat írt alá?

A jogkivonat-korlátozási beállítás konfigurálásához XML-t kell használnia a jogkivonat engedélyezési követelményeinek leírásához. A jogkivonat-korlátozás konfigurációs XML-fájljának meg kell felelnie a következő XML-sémának:
```csharp
#### Token restriction schema
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

Ha a .NET-hez készült Media Services SDK-t használja, akkor a TokenRestrictionTemplate osztály használatával létrehozhatja a korlátozási jogkivonatot.
Az alábbi példa jogkivonat-korlátozást tartalmazó engedélyezési házirendet hoz létre. Ebben a példában az ügyfélnek olyan tokent kell bemutatnia, amely tartalmaz egy aláíró kulcsot (VerificationKey), egy jogkivonat-kiállítót és a szükséges jogcímeket.
```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }
```
#### <a name="test-token"></a>Teszt token
Ha a kulcs-engedélyezési házirendhez használt jogkivonat-korlátozás alapján szeretne jogkivonatot beolvasni, tegye a következőket:
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>PlayReady dinamikus titkosítás
A Media Services használatával konfigurálhatja azokat a jogokat és korlátozásokat, amelyeket a PlayReady DRM-futtatókörnyezetnek kényszeríteni kell, amikor egy felhasználó megpróbál lejátszani egy védett tartalmat. 

Ha a tartalmat a PlayReady-mel védik, az engedélyezési házirendben megadott egyik dolog egy XML-karakterlánc, amely meghatározza a [PlayReady-licenc sablonját](media-services-playready-license-template-overview.md). A .NET-hez készült Media Services SDK-ban a PlayReadyLicenseResponseTemplate és a PlayReadyLicenseTemplate osztályok segítenek meghatározni a PlayReady-licenc sablonját.

Ha szeretné megtudni, hogyan titkosíthatja a tartalmakat a PlayReady és a Widevine használatával, olvassa el a [PlayReady és/vagy a Widevine dinamikus közös titkosítás használata](media-services-protect-with-playready-widevine.md)című témakört.

### <a name="open-restriction"></a>Nyitott korlátozás
A megnyitási korlátozás azt jelenti, hogy a rendszer minden olyan felhasználó számára elérhetővé teszi a kulcsot, aki a kulcsot kéri. Ez a korlátozás tesztelési célokra is hasznos lehet.

Az alábbi példa egy nyílt engedélyezési házirendet hoz létre, és hozzáadja azt a tartalmi kulcshoz:

```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }
```

### <a name="token-restriction"></a>Jogkivonat-korlátozás
A jogkivonat-korlátozási beállítás konfigurálásához XML-t kell használnia a jogkivonat engedélyezési követelményeinek leírásához. A jogkivonat-korlátozás konfigurációs XML-fájljának meg kell felelnie a "jogkivonat-korlátozási séma" szakaszban látható XML-sémának.

```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }
```

Ha a kulcs-engedélyezési házirendhez használt jogkivonat-korlátozás alapján szeretne jogkivonatot beolvasni, tekintse meg a "[teszt token](#test-token)" szakaszt. 

## <a name="types-used-when-you-define-contentkeyauthorizationpolicy"></a><a id="types"></a>A ContentKeyAuthorizationPolicy definiálásához használt típusok
### <a name="contentkeyrestrictiontype"></a><a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

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

### <a name="tokentype"></a><a id="TokenType"></a>TokenType

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések
Most, hogy beállította a tartalmi kulcs engedélyezési házirendjét, tekintse meg [az eszközök kézbesítési házirendjének konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md)című témakört.
