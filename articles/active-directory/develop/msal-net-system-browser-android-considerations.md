---
title: Xamarin Android rendszerbeli böngészővel kapcsolatos megfontolások (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan használhatók a rendszerböngészők a Xamarin Android rendszeren a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4230a194fb18587a209c100a39b0924e6170502d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98063467"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>A Xamarin Android rendszerbeli böngészővel kapcsolatos szempontok a MSAL.NET használatához

Ez a cikk azt ismerteti, hogy milyen szempontokat kell figyelembe vennie, amikor az Android rendszerhez készült Microsoft Authentication Library (MSAL.NET) Xamarin használja a rendszerböngészőt.

A MSAL.NET 2.4.0 előzetes verziójától kezdődően a MSAL.NET a Chrome-tól eltérő böngészőket is támogat. A továbbiakban nincs szükség a Chrome telepítésére az Android-eszközön a hitelesítéshez.

Javasoljuk, hogy használjon egyéni lapokat támogató böngészőket. Íme néhány példa a böngészőkre:

| Egyéni lapokat támogató böngészők | Csomag neve |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com. microsoft. emmx|
|Firefox | org. mozilla. Firefox|
|Ecosia | com. ecosia. Android|
|Kiwi | com. kiwibrowser. Browser|
|Bátor | com. bátor. böngésző|

Az egyéni lapokat támogató böngészők azonosításán kívül a tesztelés azt jelzi, hogy néhány böngésző, amely nem támogatja az egyéni lapokat, a hitelesítéshez is működik. Ezen böngészők közé tartozik az Opera, az Opera Mini, a inBrowser és a Maxthon. 

## <a name="tested-devices-and-browsers"></a>Tesztelt eszközök és böngészők
A következő táblázat felsorolja a hitelesítési kompatibilitáshoz tesztelt eszközöket és böngészőket.

| Eszköz | Böngésző     |  Eredmény  | 
| ------------- |:-------------:|:-----:|
| Huawei/egy + | Chrome\* | Sikeres|
| Huawei/egy + | Edge\* | Sikeres|
| Huawei/egy + | Firefox\* | Sikeres|
| Huawei/egy + | Bátor\* | Sikeres|
| Egy + | Ecosia\* | Sikeres|
| Egy + | Kiwi\* | Sikeres|
| Huawei/egy + | Operát | Sikeres|
| Huawei | OperaMini | Sikeres|
| Huawei/egy + | Inböngésző | Sikeres|
| Egy + | Maxthon | Sikeres|
| Huawei/egy + | DuckDuckGo | Felhasználó által megszakított hitelesítés|
| Huawei/egy + | UC böngésző | Felhasználó által megszakított hitelesítés|
| Egy + | Delfin | Felhasználó által megszakított hitelesítés|
| Egy + | CM böngésző | Felhasználó által megszakított hitelesítés|
| Huawei/egy + | Nincs telepítve | AndroidActivityNotFound kivétel|

\* Egyéni lapok támogatása

## <a name="known-issues"></a>Ismert problémák

Ha a felhasználónak nincs böngészője engedélyezve az eszközön, a MSAL.NET kivételt fog kiváltani `AndroidActivityNotFound` .  
  - **Enyhítés**: kérje meg a felhasználót, hogy engedélyezzen egy böngészőt az eszközön. Olyan böngészőt javasoljon, amely támogatja az egyéni lapokat.

Ha a hitelesítés meghiúsul (például ha a hitelesítés a DuckDuckGo-val kezdődik), a MSAL.NET visszaadja `AuthenticationCanceled MsalClientException` . 
  - **Gyökérszintű probléma**: az eszközön nincs engedélyezve az egyéni lapokat támogató böngésző. A hitelesítés olyan böngészővel lett elindítva, amely nem tudta befejezni a hitelesítést. 
  - **Enyhítés**: kérje meg a felhasználót, hogy engedélyezzen egy böngészőt az eszközön. Olyan böngészőt javasoljon, amely támogatja az egyéni lapokat.

## <a name="next-steps"></a>Következő lépések
További információkért és Példákért lásd: [kiválasztás egy beágyazott webböngésző és egy rendszerböngésző között a Xamarin Android és a](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) Embedded és a [rendszer webes felhasználói felületén](msal-net-web-browsers.md#embedded-vs-system-web-ui).  