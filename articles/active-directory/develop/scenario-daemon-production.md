---
title: Webes API-kat meghívó Daemon-alkalmazás áthelyezése az éles környezetbe | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan helyezhet át egy olyan Daemon-alkalmazást, amely webes API-kat hív meg éles környezetben
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d903f04055d1607ee782bd502d99a8fd9cde87ca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578447"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Webes API-kat meghívó Daemon-alkalmazás – áttérés éles környezetbe

Most, hogy már tudja, hogyan szerezheti be és használhatja a szolgáltatás-szolgáltatás hívására szolgáló tokent, megtudhatja, hogyan helyezheti át az alkalmazást éles környezetbe.

## <a name="deployment---multitenant-daemon-apps"></a>Üzembe helyezés – több-bérlős Daemon-alkalmazások

Ha olyan Daemon-alkalmazást hoz létre, amely több bérlőn is futtatható, győződjön meg arról, hogy a bérlői rendszergazda:

- Kiépít egy egyszerű szolgáltatást az alkalmazáshoz.
- Hozzájárulást biztosít az alkalmazáshoz.

Meg kell magyaráznia ügyfeleinek, hogy miként hajtják végre ezeket a műveleteket. További információ: a teljes bérlő belefoglalásának [kérelmezése](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>Kódminták

# <a name="net"></a>[.NET](#tab/dotnet)

- Dokumentáció a következőhöz:
  - [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)-példányok.
  - [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)meghívása.
- Egyéb minták/oktatóanyagok:
  - [Microsoft-Identity-platform-Console-Daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) egy kis .net Core Daemon Console alkalmazást tartalmaz, amely megjeleníti a bérlők lekérdezési Microsoft Graph felhasználóit.

    ![Minta Daemon-alkalmazás topológiája](media/scenario-daemon-app/daemon-app-sample.svg)

    Ugyanez a minta a tanúsítványokkal való változást is szemlélteti:

    ![Minta Daemon-alkalmazás topológiája – tanúsítványok](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - a [Microsoft-Identity-platform-ASPNET-WebApp-Daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) egy ASP.net MVC-webalkalmazást tartalmaz, amely a felhasználó nevében való használat helyett az alkalmazás identitásával szinkronizálja Microsoft Graph adatait. Ez a minta a rendszergazdai engedélyezési folyamatot is szemlélteti.

    ![topology](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="java"></a>[Java](#tab/java)

Próbálja ki a rövid útmutató [beszerzése tokent, és hívja meg Microsoft Graph API-t egy Java-konzol alkalmazásból az alkalmazás identitásával](quickstart-v2-java-daemon.md).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

- További információkért lásd:
  - A [konfiguráció](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md) ismertetése
  - [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) példányának példányai
  - [GYIK](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/faq.md)
- Egyéb minták/oktatóanyagok:
  - [MSAL Node Console Daemon-minta](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console)

# <a name="python"></a>[Python](#tab/python)

Próbálja ki [, hogyan szerezzen be tokent, és hívja meg Microsoft Graph API-t egy Python-konzol alkalmazásból az alkalmazás identitásával](quickstart-v2-python-daemon.md).

---

## <a name="next-steps"></a>Következő lépések

Íme néhány hivatkozás, amely segítséget nyújt a további információk megismeréséhez:

# <a name="net"></a>[.NET](#tab/dotnet)

Próbálja ki [, hogyan szerezzen be egy tokent, és hívja meg Microsoft Graph API-t egy .net Core Console-alkalmazásból az alkalmazás identitásával](quickstart-v2-netcore-daemon.md).

# <a name="java"></a>[Java](#tab/java)

Próbálja ki a rövid útmutató [beszerzése tokent, és hívja meg Microsoft Graph API-t egy Java-konzol alkalmazásból az alkalmazás identitásával](quickstart-v2-java-daemon.md).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Próbálja ki a rövid útmutatóban a [token beszerzése és a Microsoft Graph API meghívása egy Node.js Console-alkalmazásból az alkalmazás identitását használva](quickstart-v2-nodejs-console.md).

# <a name="python"></a>[Python](#tab/python)

Próbálja ki [, hogyan szerezzen be tokent, és hívja meg Microsoft Graph API-t egy Python-konzol alkalmazásból az alkalmazás identitásával](quickstart-v2-python-daemon.md).

---
