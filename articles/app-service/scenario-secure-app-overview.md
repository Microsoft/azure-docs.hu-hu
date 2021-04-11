---
title: Oktatóanyag – biztonságos Webalkalmazás létrehozása Azure App Serviceon | Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre webalkalmazásokat Azure App Service használatával, hogyan engedélyezheti a hitelesítést, meghívja az Azure Storage-t, és meghívja a Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 04/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 5b94000ce59b3a115e53ecdcd0849b97aae552c5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221840"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Oktatóanyag: hitelesítés engedélyezése App Service-és hozzáférés-tárolóban és Microsoft Graph

Ez az oktatóanyag egy általános alkalmazási forgatókönyvet ismertet, amelyben a következőket sajátíthatja el:

- [Konfigurálhatja a webalkalmazások hitelesítését](scenario-secure-app-authentication-app-service.md) , és korlátozhatja a felhasználók hozzáférését a szervezetben. Lásd: a diagramon.
- Felügyelt identitások használatával biztonságosan hozzáférhet a webalkalmazáshoz az [Azure Storage](scenario-secure-app-access-storage.md) szolgáltatásban. Lásd: B a diagramon.
- Hozzáférés Microsoft Graph a [bejelentkezett felhasználóhoz](scenario-secure-app-access-microsoft-graph-as-user.md) vagy [a webalkalmazáshoz](scenario-secure-app-access-microsoft-graph-as-app.md) a felügyelt identitások használatával. Lásd: C a diagramon.
- [Törölje az](scenario-secure-app-clean-up-resources.md) oktatóanyaghoz létrehozott erőforrásokat.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="A Microsoft Identity platform alkalmazási forgatókönyveit bemutató ábra." border="false":::

A kezdéshez megtudhatja, hogyan engedélyezheti a hitelesítést egy webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Webes alkalmazás hitelesítésének konfigurálása](scenario-secure-app-authentication-app-service.md)
