---
title: A kudu szolgáltatás áttekintése
description: Ismerje meg a App Service és annak funkciói folyamatos üzembe helyezését biztosító motort.
ms.date: 03/17/2021
ms.topic: reference
ms.openlocfilehash: ad1456f1ca123127f3d84aa8195c99fc34872aee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609261"
---
# <a name="kudu-service-overview"></a>A kudu szolgáltatás áttekintése

A kudu a OneDrive [Azure app Service](overview.md) -alapú üzembe helyezéshez kapcsolódó számos szolgáltatás mögötti motor, valamint más üzembe helyezési módszerek, például a Dropbox és a Sync. 

## <a name="access-kudu-for-your-app"></a>Az alkalmazás kudu elérése
Amikor létrehoz egy alkalmazást, App Service létrehoz egy, a HTTPS által védett Companion-alkalmazást. Ez a kudu-alkalmazás a következő címen érhető el:

- Az alkalmazás nem elszigetelt szinten van: `https://<app-name>.scm.azurewebsites.net`
- Alkalmazás elszigetelt szinten (App Service Environment): `https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

További információ: [hozzáférés a kudu szolgáltatáshoz](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).

## <a name="kudu-features"></a>Kudu-funkciók

A kudu hasznos információkat nyújt a App Service alkalmazásról, például:

- Alkalmazásbeállítások
- Kapcsolati sztringek
- Környezeti változók
- Kiszolgálói változók
- HTTP-fejlécek

Más funkciókat is biztosít, például a következőket:

- Futtassa a parancsokat a [kudu-konzolon](https://github.com/projectkudu/kudu/wiki/Kudu-console).
- Töltse le az IIS diagnosztikai memóriaképeit vagy a Docker-naplókat.
- Kezelheti az IIS-folyamatokat és-bővítményeket.
- Üzembe helyezési webhookok hozzáadása a Windows APS-hez.
- A ZIP-telepítés felhasználói felületének engedélyezése a alkalmazásban `/ZipDeploy` .
- [Egyéni telepítési parancsfájlokat](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)hoz létre.
- Engedélyezi a hozzáférést a [Rest APIhoz](https://github.com/projectkudu/kudu/wiki/REST-API).

## <a name="more-resources"></a>További források

A kudu egy [nyílt forráskódú projekt](https://github.com/projectkudu/kudu), amelynek dokumentációja a [kudu wikiben](https://github.com/projectkudu/kudu/wiki)található.

