---
title: Xamarin Android-alkalmazások migrálása közvetítők használatával a MSAL.NET-be
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan telepíthet át olyan Xamarin Android-alkalmazásokat, amelyek a Microsoft Authenticator vagy Intune Céges portált használják a ADAL.NET-ről a MSAL.NET-re.
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 01af0e620ecb100839f7b1101e5ff9fcfc874eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206669"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>ADAL.NET és MSAL.NET közötti közvetítőt használó Android-alkalmazások migrálása

Ha a .NET-hez (ADAL.NET) és a [hitelesítési közvetítőhöz](msal-android-single-sign-on.md)jelenleg használt Xamarin Android-alkalmazással rendelkezik, akkor ideje áttérni a .net-hez Azure Active Directory készült [Microsoft Authentication Library ](msal-overview.md) -re (MSAL.net).

## <a name="prerequisites"></a>Előfeltételek

* Egy Xamarin Android-alkalmazás már integrálva van egy közvetítővel ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) vagy [Intune céges portál](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) és a ADAL.net, amelyeket át kell telepítenie a MSAL.net-be.

## <a name="step-1-enable-the-broker"></a>1. lépés: a közvetítő engedélyezése

<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-ügyfél:</td></tr>
<tr><td>
A ADAL.NET-ben a közvetítői támogatás hitelesítésen alapuló kontextusban engedélyezett.

A közvetítő meghívásához be kellett állítania a `useBroker` *igaz* értéket a `PlatformParameters` konstruktorban:

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

A platform-specifikus oldal megjelenítő kódjában az Androidhoz a `useBroker` jelzőt állítsa igaz értékre:

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

Ezután adja meg a paramétereket a beszerzési jogkivonat hívásában:

```CSharp
AuthenticationResult result =
        await
            AuthContext.AcquireTokenAsync(
                Resource,
                ClientId,
                new Uri(RedirectURI),
                platformParameters)
                .ConfigureAwait(false);
```

</td><td>
A MSAL.NET-ben a közvetítői támogatás PublicClientApplication alapon engedélyezett.

Használja a `WithBroker()` (z) paramétert (amely alapértelmezés szerint True (igaz) értékre van állítva) a Broker meghívásához:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

Ezután a AcquireToken-hívásban:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>2. lépés: tevékenység beállítása

A ADAL.NET-ben egy tevékenységbe (általában a MainActivity) adott át a PlatformParameters részeként, ahogy az [1. lépésben is látható: engedélyezze a közvetítőt](#step-1-enable-the-broker).

A MSAL.NET egy tevékenységet is használ, de a normál Android-használatban nem szükséges közvetítő nélkül. A közvetítő használatához állítsa be a tevékenységet a brókertől érkező válaszok küldéséhez és fogadásához.

<table>
<tr><td>Aktuális ADAL-kód:</td><td>MSAL-ügyfél:</td></tr>
<tr><td>
A tevékenység az Android-specifikus platform PlatformParameters lesz átadva.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

A MSAL.NET-ben két dolgot kell beállítania az Android-tevékenységhez:

1. A ben állítsa be a-t `MainActivity.cs` `App.RootViewController`  a `MainActivity` értékre, és győződjön meg arról, hogy van egy tevékenység a közvetítő hívásával.

    Ha helytelenül van beállítva, a következő hibaüzenetet kaphatja: `"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. A AcquireTokenInteractive-hívásban használja a `.WithParentActivityOrWindow(App.RootViewController)` és a pass hivatkozást a használni kívánt tevékenységre mutató hivatkozással. Ebben a példában a MainActivity fogjuk használni.

**Például:**

Az *app. cs*:

```CSharp
   public static object RootViewController { get; set; }
```

A *MainActivity. cs*:

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

A AcquireToken hívásban:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>Következő lépések

További információ az Android-specifikus szempontokról a MSAL.NET és a Xamarin használatával kapcsolatban: [konfigurációs követelmények és hibaelhárítási tippek a Xamarin Androidhoz a MSAL.net](msal-net-xamarin-android-considerations.md).