---
title: Hibák és kivételek kezelése az iOS-hez/macOS-hez készült MSAL-ben
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan kezelheti a hibákat és kivételeket, a feltételes hozzáférési jogcímeket, és próbálkozzon újra a MSAL-ben iOS/macOS-alkalmazások esetén.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, oldalton
ms.custom: aaddev
ms.openlocfilehash: f7f2abadb7586e1b19168eb46a54bad53caa05cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761100"
---
# <a name="handle-errors-and-exceptions-in-msal-for-iosmacos"></a>Hibák és kivételek kezelése az iOS-hez/macOS-hez készült MSAL-ben

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-iosmacos"></a>Hibakezelés a MSAL iOS/macOS rendszeren

Az iOS-és macOS-hibák MSAL teljes listája a [MSALError enumerálásban](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)szerepel.

A rendszer a MSAL által létrehozott összes hibát visszaadja a `MSALErrorDomain` tartományhoz.

Rendszerhibák esetén a MSAL az eredetit adja vissza `NSError` a System API-ból. Ha például a jogkivonat-beszerzés sikertelen a hálózati kapcsolat hiánya miatt, a MSAL a tartománnyal és a kóddal kapcsolatos hibát ad vissza `NSURLErrorDomain` `NSURLErrorNotConnectedToInternet` .

Javasoljuk, hogy legalább a következő két MSAL-hibát kezelje az ügyfél oldalán:

- `MSALErrorInteractionRequired`: A felhasználónak interaktív kérést kell tennie. Számos olyan feltételt okozhat, amely a hiba, például egy lejárt hitelesítési munkamenet vagy további hitelesítési követelmények iránti igényét eredményezi. A helyreállításhoz hívja meg a MSAL Interactive token Acquisition API-t. 

- `MSALErrorServerDeclinedScopes`: Néhány vagy minden hatókör visszautasítva. Döntse el, hogy csak a megadott hatókörökkel folytatja-e a műveletet, vagy állítsa le a bejelentkezési folyamatot.

> [!NOTE]
> Az `MSALInternalError` enumerálást csak referenciához és hibakereséshez kell használni. Ne próbálja meg automatikusan kezelni ezeket a hibákat futásidőben. Ha az alkalmazás bármely, a alá tartozó hibát észlel, érdemes `MSALInternalError` lehet egy általános felhasználót megjeleníteni, amely elmagyarázza, mi történt.

Például `MSALInternalErrorBrokerResponseNotReceived` azt jelenti, hogy a felhasználó nem fejezte be a hitelesítést, és manuálisan visszaküldte az alkalmazásnak. Ebben az esetben az alkalmazásnak általános hibaüzenetet kell mutatnia arról, hogy a hitelesítés nem fejeződött be, és azt sugallja, hogy újra megpróbálják hitelesíteni magukat.

Az alábbi Objective-C mintakód az ajánlott eljárásokat mutatja be néhány gyakori hiba esetén.

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Az iOS és a macOS rendszerhez készült MSAL lehetővé teszi, hogy konkrét jogcímeket kérjen az interaktív és csendes jogkivonat-beszerzési forgatókönyvekben is.

Egyéni jogcímek igényléséhez adja meg `claimsRequest` a következőt: `MSALSilentTokenParameters` vagy `MSALInteractiveTokenParameters` .

További információért tekintse meg az [egyéni jogcímek kérése az MSAL használatával iOS és MacOS](request-custom-claims.md) rendszerhez című témakört.

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Következő lépések

A hibák diagnosztizálásához és hibakereséséhez érdemes lehet engedélyezni a [MSAL for iOS/MacOS naplózását](msal-logging-ios.md) .
