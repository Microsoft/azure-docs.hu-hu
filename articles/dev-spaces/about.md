---
title: Mi az Azure Dev Spaces?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Ismerje meg, hogy az Azure dev Spaces hogyan nyújt gyors, ismétlődő Kubernetes-fejlesztési élményt az Azure Kubernetes Service-fürtökben található csapatok számára
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, kubectl, k8s
manager: gwallace
ms.openlocfilehash: fd6f52c71c81f253f3f40f05408e45b6a6c0dbce
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199475"
---
# <a name="what-is-azure-dev-spaces"></a>Mi az Azure Dev Spaces?

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Az Azure dev Spaces gyors, ismétlődő Kubernetes-fejlesztési élményt nyújt a csapatok számára az Azure Kubernetes Service (ak) fürtökben. Az Azure dev Spaces lehetővé teszi az alkalmazás összes összetevőjének hibakeresését és tesztelését az AK-ban minimális fejlesztői gép beállításával, a függőségek replikálásának vagy kigúnyolása nélkül.

![Az ábrán egy alkalmazás két verziója egymástól függetlenül lett kifejlesztve. Ezután egy Azure dev Spaces fejlesztői környezetben egyesítjük őket.](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Hogyan egyszerűsíti le az Azure Dev Spaces a Kubernetes fejlesztést

Az Azure dev Spaces lehetővé teszi a csapatok számára, hogy a Service-alkalmazás fejlesztésére és gyors iterációra összpontosítsanak azáltal, hogy a csapatok közvetlenül a teljes, AK-ban futó Service-architektúrával vagy alkalmazással működnek. Az Azure dev Spaces lehetővé teszi, hogy a rendszer elkülönítetten frissítse a szolgáltatás-architektúra egyes részeit anélkül, hogy ez hatással lenne a többi AK-fürtre vagy más fejlesztőre. Az Azure dev Spaces fejlesztésre és tesztelésre szolgál az alacsonyabb szintű fejlesztési és tesztelési környezetekben, és nem a termelési AK-fürtökön való futtatásra készült.

Mivel a csapatok a teljes alkalmazással dolgozhatnak, és együttműködnek közvetlenül az AK-ban, az Azure dev Spaces szolgáltatásban:

* A helyi gép beállításának csökkentése
* A csapat új fejlesztői számára beállított idő csökkentése
* Gyorsabb iterációval növeli a csapat sebességét
* Csökkenti a redundáns fejlesztési és integrációs környezetek számát, mivel a csapattagok megoszthatnak egy fürtöt
* Megszünteti a függőségek replikálásának vagy kimodellezésének szükségességét
* Javítja a fejlesztési csapatok és a velük dolgozó csapatok együttműködését, például a DevOps csapatait

Az Azure dev Spaces lehetővé teszi a Docker-és Kubernetes-eszközök létrehozását a projektekhez. Ez az eszköz lehetővé teszi új és meglévő alkalmazások egyszerű hozzáadását a fejlesztői és egyéb AK-fürtökhöz.

Az Azure dev Spaces működésével kapcsolatos további információkért lásd: [Az Azure dev Spaces működése és konfigurálása][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Támogatott régiók és konfigurációk

Az Azure dev-helyeket csak [bizonyos régiókban][supported-regions]található AK-fürtök támogatják. Az Azure dev Spaces támogatja az Azure [CLI](/cli/azure/install-azure-cli) vagy a [Visual Studio Code](https://code.visualstudio.com/download) használatát a Linux, MacOS vagy Windows 8 vagy újabb rendszereken telepített [Azure dev Spaces bővítménnyel](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) , hogy az alkalmazásait az AK-ban hozza létre és futtassa. Emellett támogatja a Windows rendszeren telepített [Visual Studio 2019](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) Azure-fejlesztési számítási feladattal való használatát is.

## <a name="next-steps"></a>Következő lépések

További információ az Azure dev Spaces működéséről.

> [!div class="nextstepaction"]
> [Az Azure Dev Spaces működése](how-dev-spaces-works.md)

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
