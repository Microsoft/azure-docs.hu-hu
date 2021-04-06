---
title: Hibák és kivételek naplózása a MSAL iOS/macOS rendszeren
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan naplózhat hibákat és kivételeket a MSAL iOS/macOS rendszerhez
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: ee3837b75d586238e7ca6ac85434cc56f592929d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98763400"
---
# <a name="logging-in-msal-for-iosmacos"></a>Naplózás az iOS-hez/macOS-hez készült MSAL-ben

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL iOS és macOS rendszerű naplózáshoz – ObjC

Állítsa be a visszahívást, hogy rögzítse a MSAL-naplózást, és építse be azt a saját alkalmazás naplózása során. A visszahívás aláírása így néz ki:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Például:

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>Személyes adatok

Alapértelmezés szerint a MSAL nem rögzíti vagy nem naplózza a személyes adatmennyiséget. A könyvtár lehetővé teszi, hogy az alkalmazások fejlesztői ezt a MSALLogger osztály egyik tulajdonságán keresztül kapcsolják be. A bekapcsolásával `pii.Enabled` az alkalmazás felelősséget vállal a szigorúan bizalmas adatok biztonságos kezeléséhez és a szabályozási követelmények követéséhez.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Naplózási szintek

Ha az iOS-es és macOS-es MSAL használatával jelentkezik be, a naplózási szint beállításához használja a következő értékek egyikét:

|Level  |Leírás |
|---------|---------|
| `MSALLogLevelNothing`| Az összes naplózás letiltása |
| `MSALLogLevelError` | Alapértelmezett szint, csak akkor jeleníti meg az adatokat, ha hibák történnek |
| `MSALLogLevelWarning` | Figyelmeztetések |
| `MSALLogLevelInfo` |  A könyvtár belépési pontjai, paraméterekkel és különböző kulcstartó műveletekkel |
|`MSALLogLevelVerbose`     |  API-nyomkövetés |

Például:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Napló üzenetének formátuma

A MSAL üzenetek része a következő formátumú: `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Például:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

A korrelációs azonosítók és időbélyegek megadása a problémák nyomon követéséhez hasznos. Az időbélyeg és a korrelációs azonosító információi elérhetők a napló üzenetében. Az egyetlen megbízható hely a MSAL naplózási üzeneteiből.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL iOS és macOS rendszerű naplózáshoz – Swift

Állítsa be a visszahívást, hogy rögzítse a MSAL-naplózást, és építse be azt a saját alkalmazás naplózása során. A visszahíváshoz tartozó aláírás (az Objective-C-ben) a következőképpen néz ki:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Például:

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>Személyes adatok

Alapértelmezés szerint a MSAL nem rögzíti vagy nem naplózza a személyes adatmennyiséget. A könyvtár lehetővé teszi, hogy az alkalmazások fejlesztői ezt a MSALLogger osztály egyik tulajdonságán keresztül kapcsolják be. A bekapcsolásával `pii.Enabled` az alkalmazás felelősséget vállal a szigorúan bizalmas adatok biztonságos kezeléséhez és a szabályozási követelmények követéséhez.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Naplózási szintek

Ha az iOS-es és macOS-es MSAL használatával jelentkezik be, a naplózási szint beállításához használja a következő értékek egyikét:

|Level  |Leírás |
|---------|---------|
| `MSALLogLevelNothing`| Az összes naplózás letiltása |
| `MSALLogLevelError` | Alapértelmezett szint, csak akkor jeleníti meg az adatokat, ha hibák történnek |
| `MSALLogLevelWarning` | Figyelmeztetések |
| `MSALLogLevelInfo` |  A könyvtár belépési pontjai, paraméterekkel és különböző kulcstartó műveletekkel |
|`MSALLogLevelVerbose`     |  API-nyomkövetés |

Például:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Napló üzenetének formátuma

A MSAL üzenetek része a következő formátumú: `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Például:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

A korrelációs azonosítók és időbélyegek megadása a problémák nyomon követéséhez hasznos. Az időbélyeg és a korrelációs azonosító információi elérhetők a napló üzenetében. Az egyetlen megbízható hely a MSAL naplózási üzeneteiből.

---

## <a name="next-steps"></a>Következő lépések

További példákért tekintse meg a [Microsoft Identity platform Code Samples](sample-v2-code.md)című témakört.