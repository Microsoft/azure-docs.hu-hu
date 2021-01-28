---
title: Hibák és kivételek naplózása a MSAL.NET-ben
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan naplózhatja a hibákat és a kivételeket a MSAL.NET-ben
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
ms.openlocfilehash: da36ce0a956e0c3ed369a676960bdb9b5c5b1199
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954834"
---
# <a name="logging-in-msalnet"></a>Naplózás az MSAL.NET-ben

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>Naplózás konfigurálása a MSAL.NET-ben

A 3. x MSAL a naplózás az alkalmazáson belül, a `.WithLogging` Builder módosító használatával van beállítva. Ez a metódus nem kötelező paramétereket vesz igénybe:

- `Level` lehetővé teszi a kívánt naplózási szint eldöntését. A hibák beállítása csak hibaüzeneteket kap
- `PiiLoggingEnabled` lehetővé teszi a személyes és szervezeti adatnaplózást, ha az igaz értékre van állítva. Alapértelmezés szerint ez hamis értékre van állítva, így az alkalmazás nem naplózza a személyes adatait.
- `LogCallback` egy olyan delegált értékre van beállítva, amely a naplózást végzi. Ha `PiiLoggingEnabled` az értéke igaz, a metódus kétszer kapja meg az üzeneteket: egyszer a `containsPii` paraméter értéke false (hamis), az üzenet pedig személyes adatként nem, a paraméter pedig az `containsPii` igaz értékkel jelenik meg, és az üzenet személyes adatmennyiséget is tartalmazhat. Bizonyos esetekben (ha az üzenet nem tartalmaz személyes adatfájlokat), az üzenet ugyanaz lesz.
- `DefaultLoggingEnabled` a platform alapértelmezett naplózásának engedélyezése. Alapértelmezés szerint hamis. Ha úgy állítja be az igaz értéket, hogy az asztali/UWP-alkalmazásokban az esemény-nyomkövetést használja, az iOS-és a logcat-NSLog az Androidon.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

> [!TIP]
 > Tekintse meg a [MSAL.net wikit](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) a MSAL.net-naplózási és egyéb mintákhoz.

## <a name="next-steps"></a>Következő lépések

További példákért tekintse meg a [Microsoft Identity platform Code Samples](sample-v2-code.md)című témakört.