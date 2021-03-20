---
title: Hogyan működik a Visual Studio Code az Azure dev Spaces-szel
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Ismerje meg, hogyan segíti a Visual Studio Code és az Azure dev Spaces a Kubernetes-alkalmazások hibakeresését és gyors megismétlését
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: edfcb8107280bb86144b798da2d5b1c16371528e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91960745"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Hogyan működik a Visual Studio Code az Azure dev Spaces-szel

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Használhatja a Visual Studio Code és az [Azure dev Spaces bővítményt][azds-extension] a szolgáltatások előkészítéséhez, futtatásához és hibakereséséhez az Azure dev Spaces használatával. A Visual Studio Code és az Azure dev Spaces bővítménnyel a következőket teheti:

* Eszközök előállítása a szolgáltatások futtatásához és hibakereséséhez az AK-ban
* Java-, Node.js-és .NET Core-szolgáltatások futtatása fejlesztői térben
* A Java-, Node.js-és .NET Core-szolgáltatások közvetlen hibakeresése egy fejlesztői térben

## <a name="generate-assets"></a>Eszközök előállítása

A Visual Studio Code és az Azure dev Spaces bővítmény a következő eszközöket állítja elő a projekthez:

* Dockerfiles a Mavent, Node.js alkalmazásokat és .NET Core-alkalmazásokat használó Java-alkalmazásokhoz
* Helm-diagramok szinte bármilyen nyelvhez egy Docker
* Egy `azds.yaml` fájl, amely a projekthez tartozó [Azure dev Spaces konfigurációs fájl][azds-yaml]
* Egy olyan `.vscode` mappa, amelyen a Visual Studio Code elindítja a projekt Java-alkalmazásokhoz készült konfigurációját a Maven, Node.js alkalmazások és a .net Core-alkalmazások használatával

A Docker, a Helm diagram és a `azds.yaml` fájlok ugyanazokat az eszközöket generálják, amikor futnak `azds prep` . Ezek a fájlok a Visual Studio code-on kívül is használhatók a projekt AK-ban való futtatására, például a futtatásra `azds up` . A `.vscode` mappát csak a Visual Studio Code használja, hogy a projektet a Visual Studio Code-ból futtassa az AK-ban.

## <a name="run-your-service-in-aks"></a>A szolgáltatás futtatása AK-ban

Miután létrehozta a projekthez tartozó eszközöket, futtathatja Java-, Node.js-és .NET Core-szolgáltatásait a Visual Studio Code-ból egy meglévő fejlesztői térben. A Visual Studio Code *hibakeresés* lapján megnyithatja a konfiguráció elindítása a `.vscode` címtárból a projekt futtatásához.

Létre kell hoznia az AK-fürtöt, és engedélyeznie kell az Azure dev-helyeket a fürtön a Visual Studio code-on kívül. Újra felhasználhatja a meglévő Dockerfiles, Helm-diagramokat és a `azds.yaml` Visual Studio code-on kívül létrehozott fájlokat, például a futtatásával létrehozott eszközöket `azds prep` . Ha nem a Visual Studio code-on kívül generált eszközöket használ fel, akkor továbbra is rendelkeznie kell egy `.vscode` könyvtárral. Ezt a `.vscode` könyvtárat a Visual Studio Code és az Azure dev Spaces bővítmény újra létrehozhatja, és nem írja felül a meglévő eszközöket.

A .NET Core-projektek esetében telepíteni kell a [C#-bővítményt][csharp-extension] , hogy a .NET-szolgáltatást a Visual Studio Code-ból futtassa. A Mavent használó Java-projektekhez is rendelkeznie kell a [Java-hibakeresővel az Azure dev Spaces bővítménnyel][java-extension] , valamint a [telepített és konfigurált Maven][maven] használatával, hogy a Java-szolgáltatás a Visual Studio Code-ból fusson.

## <a name="debug-your-service-in-aks"></a>Szolgáltatás hibakeresése az AK-ban

A projekt elindítása után a Java-, Node.js-és .NET Core-szolgáltatásait közvetlenül a Visual Studio Code-ból futtathatja a fejlesztői térben. A könyvtár indítási konfigurációja `.vscode` további hibakeresési információkat biztosít a szolgáltatás futtatásához, ha a hibakeresés engedélyezve van egy fejlesztői térben. A Visual Studio Code a dev Spaces-ben futó tárolóban is kapcsolódik a hibakeresési folyamathoz, amely lehetővé teszi a töréspontok beállítását, a változók vizsgálatát és más hibakeresési műveletek elvégzését.

## <a name="next-steps"></a>Következő lépések

További információ az Azure dev Spaces működéséről.

> [!div class="nextstepaction"]
> [Az Azure Dev Spaces működése](how-dev-spaces-works.md)

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
