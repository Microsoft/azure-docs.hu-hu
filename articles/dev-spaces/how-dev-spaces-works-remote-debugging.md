---
title: A kód távoli hibakeresése az Azure dev Spaces működésével
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Ismerteti az Azure Kubernetes Service-ben az Azure dev Spaces szolgáltatással végzett távoli hibakeresés folyamatait
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: 0487b80d23974a66bafe93ee1fbdf9b796d0ab53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91975042"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>A kód távoli hibakeresése az Azure dev Spaces működésével

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Az Azure dev Spaces lehetővé teszi a Kubernetes-alkalmazások gyors megismétlését és hibakeresését, valamint a csapattal való együttműködését egy Azure Kubernetes szolgáltatásbeli (ak-beli) fürtön. Miután a projekt egy fejlesztői térben fut, az Azure dev Spaces lehetővé teszi, hogy egy futó alkalmazást az AK-ban csatoljon és hibakeresést végezzen.

Ez a cikk bemutatja, hogyan működik a távoli hibakeresés a dev Spaces szolgáltatással.

## <a name="debug-your-code"></a>A kód hibakeresése

A Java, a .NET Core és a Node.js alkalmazások esetében a Visual Studio Code vagy a Visual Studio használatával közvetlenül a fejlesztői térben futtathatja alkalmazásait. A Visual Studio Code és a Visual Studio eszközöket biztosít a fejlesztői területhez való kapcsolódáshoz, az alkalmazás elindításához és egy hibakereső csatolásához. A Futtatás után `azds prep` megnyithatja a projektet a Visual Studio Code vagy a Visual Studio alkalmazásban. A Visual Studio Code vagy a Visual Studio létrehozza a saját konfigurációs fájljait a csatlakozáshoz, amely elkülönül a futtatástól `azds prep` . A Visual Studio Code-ból vagy a Visual studióból töréspontokat állíthat be, és elindíthatja az alkalmazást a fejlesztői területére.

![A kód hibakeresése](media/get-started-node/debug-configuration-nodejs2.png)

Ha a Visual Studio Code vagy a Visual Studio használatával indítja el az alkalmazást a hibakereséshez, a rendszer a futtatással megegyező módon kezeli az indítást és a csatlakozást a fejlesztői területhez `azds up` . Emellett a Visual Studio Code-ban és a Visual Studióban található ügyféloldali eszközök mindegyike egy további, a hibakereséshez szükséges adatokat tartalmazó paramétert biztosít. A paraméter tartalmazza a hibakereső rendszerképének nevét, a hibakereső lemezképében található hibakereső helyét, valamint az alkalmazás tárolójában lévő célhelyet a hibakereső mappa csatlakoztatásához.

A hibakereső rendszerképét a rendszer automatikusan meghatározza az ügyféloldali eszközkészlet alapján. A futtatáskor a Docker és a Helm diagram létrehozásakor használt metódushoz hasonló módszert használ `azds prep` . Miután a hibakereső csatlakoztatva van az alkalmazás rendszerképéhez, az a használatával fut `azds exec` .

## <a name="next-steps"></a>Következő lépések

További információ az Azure dev Spaces működéséről.

> [!div class="nextstepaction"]
> [Az Azure Dev Spaces működése](how-dev-spaces-works.md)
