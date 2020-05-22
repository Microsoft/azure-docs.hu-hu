---
title: Webes API-kat meghívó asztali alkalmazás létrehozása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó asztali alkalmazást (áttekintés)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 92f0909660427e414264442523dba3ed2abe0142
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771859"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Forgatókönyv: webes API-kat meghívó asztali alkalmazás

A webes API-kat meghívó asztali alkalmazások létrehozásához szükséges tudnivalók.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Bevezetés

Ha még nem tette meg, hozza létre első alkalmazását a .NET Desktop rövid útmutatójának, a Univerzális Windows-platform (UWP) rövid útmutatójának vagy a macOS natív alkalmazásának rövid útmutatójának használatával:

> [!div class="nextstepaction"]
> [Rövid útmutató: Jogkivonat lekérése és a Microsoft Graph API meghívása egy Windows asztali alkalmazásból](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Gyors útmutató: token beszerzése és Microsoft Graph API meghívása egy UWP-alkalmazásból](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Gyors útmutató: token beszerzése és Microsoft Graph API meghívása macOS natív alkalmazásból](./quickstart-v2-ios.md)

## <a name="overview"></a>Áttekintés

Írhat egy asztali alkalmazást, és a felhasználók bejelentkezhetnek az alkalmazásba, és meghívhatják a webes API-kat, például a Microsoft Graph, más Microsoft API-kat vagy a saját webes API-t. Több lehetősége van:

- Használhatja az interaktív jogkivonat-beszerzést:

  - Ha az asztali alkalmazás támogatja a grafikus vezérlőket, például egy Windows. Form alkalmazást, egy WPF-alkalmazást vagy egy macOS natív alkalmazást.
  - Ha pedig .NET Core-alkalmazás, és Ön vállalja, hogy a Azure Active Directory (Azure AD) hitelesítési interakciót végez a rendszerböngészőben.

- A Windowsban üzemeltetett alkalmazásokhoz a Windows-tartományhoz vagy az Azure AD-hez csatlakoztatott számítógépeken futó alkalmazások esetében is lehetséges a jogkivonat csendes beszerzése az integrált Windows-hitelesítés használatával.
- Végül, és bár nem ajánlott, használhat egy felhasználónevet és egy jelszót a nyilvános ügyfélalkalmazások számára. Bizonyos helyzetekben, például a DevOps-ben továbbra is szükség van. A használata korlátozásokat alkalmaz az alkalmazására. Például nem tud bejelentkezni egy olyan felhasználóra, akinek [többtényezős hitelesítést](../authentication/concept-mfa-howitworks.md) kell végrehajtania (feltételes hozzáférés). Az alkalmazás nem használhatja az egyszeri bejelentkezést (SSO).

  Emellett a modern hitelesítés alapelvei is megtalálhatók, és csak az örökölt okok miatt biztosítható.

  ![Asztali alkalmazás](media/scenarios/desktop-app.svg)

- Ha olyan hordozható parancssori eszközt ír, amely valószínűleg Linux vagy Mac rendszerű .NET Core-alkalmazás, és ha elfogadja, hogy a hitelesítés delegálásra kerül a rendszerböngészőbe, interaktív hitelesítést használhat. A .NET Core nem biztosít [webböngészőt](https://aka.ms/msal-net-uses-web-browser), így a hitelesítés a rendszerböngészőben történik. Ellenkező esetben a legjobb megoldás az, ha az eszköz kód áramlását használja. Ez a folyamat böngésző nélküli alkalmazásokhoz is használatos, például IoT alkalmazásokhoz.

  ![Böngészővel nem rendelkező alkalmazás](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Sajátosságai

Az asztali alkalmazások számos sajátossággal rendelkeznek. Főleg attól függnek, hogy az alkalmazás interaktív hitelesítést használ-e, vagy sem.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Asztali alkalmazás: alkalmazás regisztrálása](scenario-desktop-app-registration.md)
