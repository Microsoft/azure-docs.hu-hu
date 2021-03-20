---
title: Identitás-szolgáltatók konfigurálása (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan használhatja a különböző hatóságokat, például a B2C-t, a szuverén felhőket és a vendég felhasználókat az iOS-és macOS-MSAL.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d8a176fff0da932d0fafd40b9ab895b635acc5f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96169443"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Útmutató: az iOS-és macOS-MSAL konfigurálása különböző identitás-szolgáltatók használatához

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az iOS és macOS rendszerhez készült Microsoft Authentication Library alkalmazást (MSAL) különböző, például a Azure Active Directory (Azure AD), a vállalat és a fogyasztó (B2C), a szuverén felhők és a vendég felhasználók számára.  Ebben a cikkben általában úgy gondolja, hogy a szolgáltatót identitás-szolgáltatóként tekinti.

## <a name="default-authority-configuration"></a>Alapértelmezett szolgáltató konfigurációja

`MSALPublicClientApplication` a az alapértelmezett szolgáltatói URL-címével van konfigurálva `https://login.microsoftonline.com/common` , amely a legtöbb Azure Active Directory (HRE) forgatókönyvhöz megfelelő. Hacsak nem végez speciális forgatókönyveket, például a nemzeti felhőket, vagy a B2C-vel dolgozik, nem kell módosítania.

> [!NOTE]
> A Active Directory összevonási szolgáltatások (AD FS) as Identity Provider (ADFS) szolgáltatással való modern hitelesítés nem támogatott (lásd: [ADFS for Developers](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) for details). Az ADFS-t az összevonás támogatja.

## <a name="change-the-default-authority"></a>Az alapértelmezett szolgáltató módosítása

Bizonyos helyzetekben, például a vállalatok közötti (B2C) megoldásban előfordulhat, hogy módosítania kell az alapértelmezett szolgáltatót.

### <a name="b2c"></a>B2C

A B2C-vel való együttműködéshez a [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) más szolgáltatói konfigurációt igényel. A MSAL egy szolgáltatói URL-formátumot is felismer a B2C-ként. A B2C-hatóság felismert formátuma `https://<host>/tfp/<tenant>/<policy>` például a következő: `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy` . Más támogatott B2C-szolgáltatói URL-címeket is használhat, ha a szolgáltatót B2C-szolgáltatóként deklarálja.

A B2C tetszőleges URL-formátumának támogatásához `MSALB2CAuthority` tetszőleges URL-címmel állítható be, például a következő módon:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Az alapértelmezett B2C-szolgáltatói formátumot nem használó B2C-hatóságokat ismert hatóságként kell deklarálni.

Adja hozzá az összes különböző B2C-szolgáltatót az ismert hatóságok listájához, még akkor is, ha a hatóságok csak a szabályzatban különböznek.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Ha az alkalmazás új szabályzatot kér, a szolgáltatói URL-címet módosítani kell, mert a szolgáltatói URL-cím különbözik az egyes házirendek esetében. 

A B2C-alkalmazások konfigurálásához a `@property MSALAuthority *authority` következőt kell beállítania `MSALB2CAuthority` a `MSALPublicClientApplicationConfig` létrehozása előtt `MSALPublicClientApplication` :

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Független felhők

Ha az alkalmazás szuverén felhőben fut, előfordulhat, hogy módosítania kell a szolgáltatói URL-címet a alkalmazásban `MSALPublicClientApplication` . A következő példa a szolgáltatói URL-címet állítja be a német HRE-felhővel való együttműködéshez:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Előfordulhat, hogy különböző hatóköröket kell átadnia az egyes szuverén felhőknek. A küldeni kívánt hatókörök a használt erőforrástól függenek. Használhatja például `"https://graph.microsoft.com/user.read"` a globális felhőben és `"https://graph.microsoft.de/user.read"` a német felhőben is.

### <a name="signing-a-user-into-a-specific-tenant"></a>Felhasználó aláírása egy adott bérlőn

Ha a szolgáltatói URL-cím be van állítva `"login.microsoftonline.com/common"` , a felhasználó be lesz jelentkezve a saját bérlőbe. Előfordulhat azonban, hogy egyes alkalmazásoknak egy másik bérlőbe kell írniuk a felhasználót, és egyes alkalmazások csak egyetlen Bérlővel működnek.

Ha a felhasználót egy adott bérlőbe szeretné írni, konfigurálja `MSALPublicClientApplication` egy adott szolgáltatót. Például:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Az alábbiakban bemutatjuk, hogyan lehet a felhasználókat egy adott bérlőhöz aláírni:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Támogatott hatóságok

### <a name="msalauthority"></a>MSALAuthority

Az `MSALAuthority` osztály az MSAL-szolgáltató osztályainak alap absztrakt osztálya. Ne próbálkozzon a vagy a használatával létrehozott példány létrehozásával `alloc` `new` . Ehelyett hozzon létre egy alosztályt közvetlenül ( `MSALAADAuthority` ,), `MSALB2CAuthority` vagy használja a Factory metódust `authorityWithURL:error:` alosztályok létrehozásához a szolgáltatói URL-cím használatával.

A `url` tulajdonság használatával normalizált szolgáltatói URL-címet kaphat. A nem a hatóság részét képező további paraméterek és elérési utak összetevői vagy töredékei nem lesznek a visszaadott normalizált szolgáltatói URL-címben.

A következő alosztályai a `MSALAuthority` -től függően a szolgáltatótól függően hozhatók létre.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` HRE-szolgáltatót jelöl. A szolgáltatói URL-címnek a következő formátumban kell lennie, ahol a `<port>` nem kötelező: `https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` a B2C-hatóságot jelöli. Alapértelmezés szerint a B2C-szolgáltatói URL-címnek a következő formátumban kell lennie, ahol a `<port>` nem kötelező: `https://<host>:<port>/tfp/<tenant>/<policy>` . A MSAL azonban más, tetszőleges B2C-szolgáltatói formátumokat is támogat.

## <a name="next-steps"></a>Következő lépések

További információ a [hitelesítési folyamatokról és az alkalmazási forgatókönyvekről](authentication-flows-app-scenarios.md)