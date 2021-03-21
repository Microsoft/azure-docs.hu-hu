---
title: SSO ADAL & MSAL alkalmazások között (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan oszthat meg egyszeri bejelentkezést a ADAL és a MSAL alkalmazások között
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 396e9cfeace8791a59dec4a9c9c7203212f57304
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584247"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Útmutató: ADAL és MSAL alkalmazások közötti egyszeri bejelentkezés macOS és iOS rendszeren

Az iOS rendszerhez készült Microsoft Authentication Library (MSAL) az alkalmazások közötti [ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) használatával megoszthatja az SSO-állapotot. Saját tempójában is áttelepítheti alkalmazásait MSAL, így biztosítva, hogy a felhasználók továbbra is kihasználhassák az alkalmazások közötti egyszeri bejelentkezést – akár ADAL, akár MSAL-alapú alkalmazásokkal is.

Ha a MSAL SDK-val az alkalmazások közötti egyszeri bejelentkezés beállításával kapcsolatos útmutatást keres, tekintse meg a [több alkalmazás közötti insso](single-sign-on-macos-ios.md#silent-sso-between-apps)szolgáltatást. Ez a cikk a ADAL és a MSAL közötti egyszeri bejelentkezésre összpontosít.

Az egyszeri bejelentkezést végrehajtó sajátosságok az Ön által használt ADAL-verziótól függenek.

## <a name="adal-27x"></a>ADAL 2.7. x

Ez a szakasz a MSAL és a ADAL 2.7. x közötti SSO-különbségeket ismerteti

### <a name="cache-format"></a>Gyorsítótár formátuma

A ADAL 2.7. x megtekintheti a MSAL cache formátumát. A ADAL 2.7. x verziójával nem kell külön foglalkoznia a többalkalmazásos egyszeri bejelentkezéshez. Azonban vegye figyelembe a két függvénytár által támogatott fiókazonosító-különbségeket.

### <a name="account-identifier-differences"></a>Fiókazonosító-különbségek

A MSAL és a ADAL eltérő fiókazonosító használatát teszik ki. A ADAL az UPN-t használja elsődleges fiókazonosítóként. A MSAL egy nem megjeleníthető fiókot használ, amely egy objektumazonosító és egy HRE-fiókhoz tartozó bérlői azonosító, valamint `sub` más típusú fiókok esetében egy jogcím alapján működik.

Ha `MSALAccount` a MSAL eredményben egy objektumot kap, akkor a tulajdonságban egy fiókazonosító szerepel `identifier` . Az alkalmazásnak ezt az azonosítót kell használnia az ezt követő csendes kérelmekhez.

Az `identifier` `MSALAccount` objektumon kívül a nevű kitalálható azonosító is szerepel `username` . A `userId` ADAL-ben lefordítva. `username` nem tekinthető egyedi azonosítónak, és bármikor megváltozhat, így csak a ADAL-vel való visszamenőleges kompatibilitási forgatókönyvekhez használható. A MSAL támogatja a gyorsítótárazási lekérdezéseket a vagy a használatával `username` `identifier` , ahol a lekérdezés a alapján `identifier` javasolt.

A következő táblázat összefoglalja a ADAL és a MSAL közötti fiókazonosító-különbségeket:

| Fiókazonosító                | MSAL                                                         | ADAL 2.7. x      | Régebbi ADAL (az ADAL 2.7. x előtt) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| nem játszható azonosító            | `username`                                                   | `userId`        | `userId`                       |
| egyedi, nem játszható azonosító | `identifier`                                                 | `homeAccountId` | N/A                            |
| Nem ismert fiókazonosító               | Az összes fiók lekérdezése `allAccounts:` az API-n keresztül `MSALPublicClientApplication` | N.A.             | N.A.                            |

Ez az `MSALAccount` azonosítókat biztosító interfész:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>Egyszeri bejelentkezés a MSAL-ből a ADAL-be

Ha rendelkezik egy MSAL-alkalmazással és egy ADAL-alkalmazással, és a felhasználó először bejelentkezik a MSAL-alapú alkalmazásba, akkor a ADAL-alkalmazásban lévő egyszeri bejelentkezést az `username` `MSALAccount` objektumból mentheti, majd átadja a ADAL-alapú alkalmazásnak `userId` . A ADAL a fiók adatait csendesen megtalálhatja az `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` API-val.

### <a name="sso-from-adal-to-msal"></a>Egyszeri bejelentkezés a ADAL-ből a MSAL-be

Ha rendelkezik egy MSAL-alkalmazással és egy ADAL-alkalmazással, és a felhasználó először bejelentkezik a ADAL-alapú alkalmazásba, akkor a MSAL ADAL felhasználói azonosítóit használhatja a fiókok kereséséhez. Ez akkor is érvényes, ha a ADAL-ről MSAL-re végez áttelepítést.

#### <a name="adals-homeaccountid"></a>ADAL homeAccountId

A ADAL 2.7. x a következő `homeAccountId` `ADUserInformation` tulajdonságon keresztül adja vissza az objektumot az eredményben:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId` a ADAL a `identifier` MSAL-ben egyenértékű. Ezt az azonosítót mentheti a MSAL-ben való használatra az API-val való fiókok keresésekor `accountForIdentifier:error:` .

#### <a name="adals-userid"></a>ADAL `userId`

Ha a `homeAccountId` nem érhető el, vagy csak a megtekinthető azonosítóval rendelkezik, a ADAL segítségével `userId` megkeresheti a FIÓKOT a MSAL-ben.

A MSAL-ben először keressen egy fiókot a `username` vagy a használatával `identifier` . Mindig `identifier` a lekérdezéshez használja, ha rendelkezik vele, és csak `username` tartalékként használja. Ha a rendszer megtalálta a fiókot, használja a `acquireTokenSilent` hívásokat a hívásokban.

Objective-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL támogatott fiókok keresési API-jai:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2. x – 2.6.6

Ez a szakasz a MSAL és a ADAL 2. x-2.6.6 közötti SSO-különbségeket ismerteti.

A régebbi ADAL-verziók nem támogatják natív módon a MSAL-gyorsítótár formátumát. A ADAL-ről a MSAL-re történő zökkenőmentes áttelepítés biztosítása érdekében azonban a MSAL a korábbi ADAL-gyorsítótár formátumát is beolvashatja anélkül, hogy újra kellene kérnie a felhasználói hitelesítő adatokat.

Mivel a `homeAccountId` nem érhető el a régebbi ADAL-verziókban, a következő paranccsal kell megkeresnie a fiókokat `username` :

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Például:

Objective-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Azt is megteheti, hogy beolvassa az összes fiókot, amely a ADAL is beolvassa a fiók adatait:

Objective-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Következő lépések

További információ a [hitelesítési folyamatokról és az alkalmazási forgatókönyvekről](authentication-flows-app-scenarios.md)
