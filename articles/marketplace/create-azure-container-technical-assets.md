---
title: Az Azure Container technikai eszközeinek előkészítése
description: Technikai erőforrások és irányelvek a tárolók Azure Marketplace-en való konfigurálásának megkönnyítéséhez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 014bcd6fc519c267cdf17e9e98b850425c25ead6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459334"
---
# <a name="prepare-your-azure-container-technical-assets"></a>Az Azure Container technikai eszközeinek előkészítése

Ez a cikk az Azure Marketplace-en elérhető tárolók létrehozására szolgáló technikai forrásokat és javaslatokat tartalmaz.

## <a name="before-you-begin"></a>Előkészületek

A gyors útmutatókkal, oktatóanyagokkal és mintákkal kapcsolatban tekintse meg a [Azure Container instances dokumentációját](../container-instances/index.yml).

## <a name="fundamental-technical-knowledge"></a>Alapvető műszaki ismeretek

Ezeknek az eszközöknek a tervezése, fejlesztése és tesztelése időt vesz igénybe, és technikai ismeretekre van szüksége az Azure platformról és az ajánlat létrehozásához használt technológiákról.

A megoldás tartományán kívül a mérnöki csapatnak ismernie kell a következő Microsoft-technológiákkal kapcsolatos ismereteket:

- Az Azure- [szolgáltatások](https://azure.microsoft.com/services/) alapszintű ismertetése
- [Azure-alkalmazások tervezése és Architect](https://azure.microsoft.com/solutions/architecture/)
- Az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), az [Azure Storage](https://azure.microsoft.com/services/?filter=storage)és az [Azure hálózatkezelésének](https://azure.microsoft.com/services/?filter=networking) gyakorlati ismerete
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) működésének ismerete
- A [JSON](https://www.json.org/)működésének ismerete.

## <a name="suggested-tools"></a>Javasolt eszközök

A tároló rendszerképének kezeléséhez válasszon egyet vagy mindkettőt a következő parancsfájl-környezetek közül:

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure/)-vel.

Javasoljuk, hogy adja hozzá ezeket az eszközöket a fejlesztési környezethez:

- [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Kiterjesztés: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Kiterjesztés: [szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Kiterjesztés: [SZÉPÍT JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Tekintse át az elérhető eszközöket az [Azure fejlesztői eszközök](https://azure.microsoft.com/) oldalon. Ha a Visual studiót használja, tekintse át a [Visual Studio Marketplace](https://marketplace.visualstudio.com/)-en elérhető eszközöket.

## <a name="create-the-container-image"></a>A tároló rendszerképének létrehozása

Helyszíni beállításjegyzékből nem lehet lemezképet telepíteni Azure Container Instances.

- Ha már rendelkezik egy működő tárolóval a helyi beállításjegyzékben, hozzon létre egy Azure-beállításjegyzéket, és töltse fel a tároló képét a Azure Container Registryba. További információ: [oktatóanyag: tároló-lemezképek létrehozása és üzembe helyezése a felhőben Azure Container Registry feladatokkal](../container-registry/container-registry-tutorial-quick-task.md).

- Ha még nem rendelkezik tároló-lemezképpel, és meg kell tárolóba helyezése a meglévő alkalmazást, vagy létre kell hoznia egy új tároló-alapú alkalmazást, akkor a GitHubról kell megnyitnia az alkalmazás forráskódját, létre kell hoznia egy tároló rendszerképet az alkalmazás forrásáról, és tesztelni kell egy helyi Docker-környezetben. További információ: [oktatóanyag: tároló rendszerképének létrehozása a Azure Container instances való üzembe helyezéshez](../container-instances/container-instances-tutorial-prepare-app.md).

## <a name="next-steps"></a>Következő lépések

- [A tároló ajánlat létrehozása](create-azure-container-offer.md)