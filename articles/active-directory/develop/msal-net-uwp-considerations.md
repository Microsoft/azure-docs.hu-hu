---
title: UWP megfontolások (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Tudnivalók a Univerzális Windows-platform (UWP) használatához a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 502bbe65cdc0aef768ff4f017b1f6a920815b001
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118858"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>A Univerzális Windows-platform és a MSAL.NET használatának szempontjai
A MSAL.NET-t (UWP) Univerzális Windows-platform használó alkalmazások fejlesztőinek figyelembe kell venniük a jelen cikkben ismertetett fogalmakat.

## <a name="the-usecorporatenetwork-property"></a>A UseCorporateNetwork tulajdonság
A Windows-futtatókörnyezet (WinRT) platformon `PublicClientApplication` a logikai tulajdonság szerepel `UseCorporateNetwork` . Ez a tulajdonság lehetővé teszi a Windows 8,1 alkalmazások és UWP alkalmazások számára az integrált Windows-hitelesítés (IWA) előnyeit, ha a felhasználó olyan fiókba van bejelentkezve, amely összevont Azure Active Directory (Azure AD) Bérlővel rendelkezik. Az operációs rendszerbe bejelentkezett felhasználók használhatnak egyszeri bejelentkezést (SSO) is. A tulajdonság beállításakor a `UseCorporateNetwork` MSAL.net egy webes hitelesítési közvetítőt (WAB) használ.

> [!IMPORTANT]
> Ha a `UseCorporateNetwork` tulajdonságot igaz értékre állítja, feltételezi, hogy az alkalmazás fejlesztője engedélyezte a IWA az alkalmazásban. A IWA engedélyezése:
> - A UWP-alkalmazás `Package.appxmanifest` **képességek** lapján engedélyezze a következő képességeket:
>   - **Vállalati hitelesítés**
>   - **Magánhálózatok (ügyfél & kiszolgáló)**
>   - **Megosztott felhasználói tanúsítvány**

A IWA alapértelmezés szerint nincs engedélyezve, mert Microsoft Store magas szintű ellenőrzést igényel, mielőtt olyan alkalmazásokat fogad el, amelyek a vállalati hitelesítés vagy a megosztott felhasználói tanúsítványok képességeit igénylik. Nem minden fejlesztő szeretné ezt az ellenőrzési szintet végrehajtani.

A UWP platformon az alapul szolgáló WAB-implementáció nem működik megfelelően olyan nagyvállalati forgatókönyvekben, ahol engedélyezve van a feltételes hozzáférés. A felhasználók a Windows Hello használatával próbálnak bejelentkezni a probléma tüneteire. Ha a felhasználó a tanúsítvány kiválasztását kéri:

- A PIN-kód tanúsítványa nem található.
- Miután a felhasználó kiválaszt egy tanúsítványt, a rendszer nem kéri a PIN-kódot.

Megpróbálhatja elkerülni ezt a problémát egy alternatív módszer, például a Felhasználónév-jelszó és a telefonos hitelesítés használatával, de a felület nem jó.

## <a name="troubleshooting"></a>Hibaelhárítás

Egyes ügyfelek a következő bejelentkezési hibát jelentették be bizonyos vállalati környezetekben, ahol tudják, hogy internetkapcsolattal rendelkeznek, és a kapcsolatok nyilvános hálózattal működnek.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

A probléma elkerüléséhez győződjön meg arról, hogy a WAB (a mögöttes Windows-összetevő) engedélyezi a magánhálózaton. Ezt a beállításkulcs beállításával teheti meg:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

További információ: [web Authentication Broker-Hegedűs](/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>További lépések
A következő minták további információkat nyújtanak.

Sample | Platform | Leírás 
|------ | -------- | -----------|
|[Active-Directory-DotNet-Native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | A MSAL.NET-t használó UWP ügyfélalkalmazás. A Microsoft Graph egy Azure AD 2,0-végponttal hitelesítő felhasználóhoz fér hozzá. <br>![Topológia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[Active-Directory-xamarin-Native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Egy egyszerű Xamarin űrlapos alkalmazás, amely bemutatja, hogyan hitelesítheti a Microsoft személyes fiókjait és az Azure AD-t az Azure AD 2,0-végponton keresztül a MSAL használatával. Azt is bemutatja, hogyan lehet elérni Microsoft Graph és megjeleníti az eredményül kapott jogkivonatot. <br>![Topológia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|