---
title: Keresse meg az Azure Service Fabric-háló minták |} A Microsoft Docs
description: Ismerje meg, hogyan találhatja meg a különböző Service Fabric-háló mintaalkalmazások.
services: service-fabric-mesh
keywords: ''
author: v-vasuke
ms.author: v-vasuke
ms.date: 12/03/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: db8c68bf5f9aeb8069044c1344be9f69e498b1b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419560"
---
# <a name="find-service-fabric-mesh-samples"></a>Service Fabric háló minták keresése

Ez a táblázat ismerteti az elérhető a Service Fabric-háló mintaalkalmazások. Ezekben a példákban a forráskód bemutatja, hogyan érhet el egy adott forgatókönyv a Service Fabric erőforrás modell használatával.

Közvetlenül az Azure-ban sablonok telepítésével kapcsolatos további információkért lásd: a [GitHub mintasablon lapot.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)


|Mintául szolgáló sablon|Forgatókönyv leírása|Forráskód|Fejlesztői eszközök|
|------------|--------------------|----------|----------------------|
| [Hello World App](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Tárolóban lévő üzemeltetett statikus weblapon. Linux esetén használ nginx-et, Windows IIS-hez | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Nincsenek követelmények |
| [Az Azure-Fájlkötetek számláló alkalmazás](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter) | Store állapot csatlakoztatja az Azure Files-alapú mennyiségi tárolón belül. <br><br> **Megjegyzés:** Ehhez a sablonhoz szükséges egy már ki kell építeni az Azure Files fájlmegosztás [utasításokat](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | A Visual Studio eszközök háló |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Hozzon létre egy előtér- és háttérportokat szolgáltatás által használt névfeloldási DNS-alapú alkalmazáshoz. Oktatóanyag keretében használt [Itt](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | A Visual Studio eszközök háló |
| [Vizuális objektumok alkalmazás](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Méretezési csoport és a frissítés mikroszolgáltatások az alkalmazáson belül. | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  A Visual Studio eszközök háló |
| [Szavazóalkalmazás](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Egy előtér- és háttérportokat szolgáltatás által használt névfeloldási DNS-alapú alkalmazás létrehozása | [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | A Visual Studio háló a Windows-verzióhoz tartozó eszközöket, a VS Code / dotnet cli is használható a Linux-verzió |
| [A számláló App Service Fabric Reliable kötetek](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter)| Service Fabric Reliable lemez csatlakoztatásával Store állapota alapján kötet tárolón belül.| [Forráskód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | A Visual Studio eszközök háló |
