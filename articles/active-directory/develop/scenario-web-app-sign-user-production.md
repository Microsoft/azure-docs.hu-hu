---
title: A felhasználók által az éles környezetbe bejelentkező webes alkalmazás áthelyezése | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre egy webalkalmazást, amely aláírja a felhasználókat (éles környezetbe helyezi)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f670af1fca4b4638988e53075f092ca1bbac55b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578260"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Felhasználók számára bejelentkező webalkalmazás: áthelyezés éles környezetbe

Most, hogy tudja, hogyan szerezhet be tokent a webes API-k meghívásához, itt néhány megfontolandó szempontot figyelembe kell venni, amikor az alkalmazást éles környezetbe helyezi.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>Hibaelhárítás
Amikor a felhasználók első alkalommal jelentkeznek be a webalkalmazásba, hozzá kell járulniuk. Egyes szervezeteknél azonban a felhasználók a következőhöz hasonló üzenetet láthatnak: a *AppName engedélyre van szüksége a szervezet erőforrásaihoz való hozzáféréshez, melyeket csak rendszergazda adhat meg. Kérje meg a rendszergazdát, hogy adjon engedélyt az alkalmazásnak, mielőtt használni lehetne.*
Ennek az az oka, hogy a bérlői rendszergazdája **letiltotta** a felhasználók beleegyező jogosultságát. Ebben az esetben lépjen kapcsolatba a bérlői rendszergazdával, hogy az alkalmazás által igényelt hatókörökhöz rendszergazdai jogosultságot adjanak.

## <a name="same-site"></a>Ugyanazon a helyen

Győződjön meg arról, hogy a Chrome böngésző új verzióival kapcsolatos lehetséges problémákat tapasztalja: [hogyan kezelheti a SameSite-cookie-változásokat a Chrome böngészőben](howto-handle-samesite-cookie-changes-chrome-browser.md).

A Microsoft. Identity. Web NuGet csomag kezeli a leggyakoribb SameSite problémákat.

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Deep Dive: ASP.NET Core webalkalmazás-oktatóanyag

További információ a felhasználók bejelentkezésének egyéb módjairól a ASP.NET Core oktatóanyagban: 

[A webalkalmazások bejelentkezhetnek a felhasználókba, és meghívhatják az API-kat a Microsoft Identity platform for Developers szolgáltatással](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Ez a progresszív oktatóanyag éles használatra kész kóddal rendelkezik a webalkalmazásokhoz, többek között a bejelentkezéshez a következő fiókokkal:

- A szervezet
- Több szervezet
- Munkahelyi vagy iskolai fiókok, vagy személyes Microsoft-fiókok
- [Azure AD B2C](../../active-directory-b2c/overview.md)
- Országos felhők

## <a name="tutorial-nodejs-web-app"></a>Oktatóanyag: Node.js webalkalmazás

További információ a Node.js Web-ről ebben az oktatóanyagban:

[Oktatóanyag: bejelentkezési felhasználók Node.js & Express-webalkalmazásban](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-nodejs-webapp-msal)

## <a name="sample-code-java-web-app"></a>Mintakód: Java-webalkalmazás

További információ a Java-webalkalmazásról ebből a mintából a GitHubon: 

[Egy Java-webalkalmazás, amely a Microsoft Identity platformmal és a hívások Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

## <a name="next-steps"></a>Következő lépések

Miután a webalkalmazás bejelentkezett a felhasználókba, a webes API-kat hívhat a bejelentkezett felhasználók nevében. A webes API-k hívása a webalkalmazásból a következő forgatókönyv objektuma: [webalkalmazás, amely webes API-kat hív](scenario-web-app-call-api-overview.md)meg.