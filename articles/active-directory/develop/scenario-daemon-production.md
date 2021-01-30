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
ms.openlocfilehash: 04ee4ccc3b0553d6532869d6403000fdedd93b5f
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062711"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Webes API-kat meghívó Daemon-alkalmazás – áttérés éles környezetbe

Most, hogy már tudja, hogyan szerezheti be és használhatja a szolgáltatás-szolgáltatás hívására szolgáló tokent, megtudhatja, hogyan helyezheti át az alkalmazást éles környezetbe.

## <a name="deployment---multitenant-daemon-apps"></a>Üzembe helyezés – több-bérlős Daemon-alkalmazások

Ha olyan Daemon-alkalmazást hoz létre, amely több bérlőn is futtatható, meg kell győződnie arról, hogy a bérlői rendszergazda:

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

---

## <a name="next-steps"></a>Következő lépések

Íme néhány hivatkozás, amely segítséget nyújt a további információk megismeréséhez:

# <a name="python"></a>[Python](#tab/python)

Próbálja ki [, hogyan szerezzen be tokent, és hívja meg Microsoft Graph API-t egy Python-konzol alkalmazásból az alkalmazás identitásával](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

Próbálja ki a rövid útmutató [beszerzése tokent, és hívja meg Microsoft Graph API-t egy Java-konzol alkalmazásból az alkalmazás identitásával](./quickstart-v2-java-daemon.md).

---
