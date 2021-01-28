---
title: Egyoldalas alkalmazás áthelyezése éles környezetbe
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást (áttérés éles környezetbe)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954960"
---
# <a name="single-page-application-move-to-production"></a>Egyoldalas alkalmazás: áthelyezés éles környezetbe

Most, hogy már tudja, hogyan szerezzen be tokent a webes API-k meghívásához, itt néhány megfontolandó szempontot figyelembe kell venni, amikor az alkalmazást éles környezetbe helyezi.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>Az alkalmazás üzembe helyezése

Tekintse meg az [üzembe helyezési mintát](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) , amelyből megtudhatja, hogyan helyezheti üzembe a Spa-és webes API-projektjeit az Azure Storage és az Azure app Services használatával. 

## <a name="code-samples"></a>Kódminták

Ezek a kódok több fő műveletet mutatnak be egy egyoldalas alkalmazáshoz.
- [SPA ASP.net-háttérrel](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi): a saját háttérbeli webes API-k (ASP.net Core) jogkivonatait **MSAL.js** használatával szerezheti be.

- [Node.js web API (Azure ad](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2): a háttérbeli webes API (Node.js) hozzáférési jogkivonatának ellenőrzése a **Passport-Azure-ad** használatával.

- [Spa és Azure ad B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp): a **MSAL.js** használata a **Azure Active Directory B2C** (Azure ad B2C) szolgáltatásban regisztrált alkalmazásokban való bejelentkezéshez.

- [Node.js web API (Azure ad B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi): a **Passport-Azure-AD** használata a **Azure Active Directory B2C** (Azure ad B2C) szolgáltatásban regisztrált alkalmazások hozzáférési jogkivonatának érvényesítéséhez.

## <a name="next-steps"></a>Következő lépések

- [JavaScript Spa oktatóanyag](./tutorial-v2-javascript-spa.md): részletes útmutató a felhasználók bejelentkezéséhez és hozzáférési token beszerzéséhez a **Microsoft Graph API** **MSAL.js** használatával történő meghívásához.