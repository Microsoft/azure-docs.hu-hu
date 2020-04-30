---
title: A Visual Studio Code használata az Azure Blockchain szolgáltatáshoz való kapcsolódáshoz
description: Kapcsolódás Azure Blockchain Service Consortium-hálózathoz a Visual Studio Code-ban található Ethereum-bővítményhez készült Azure Blockchain Development Kit használatával
ms.date: 04/22/2020
ms.topic: quickstart
ms.reviewer: caleteet
ms.openlocfilehash: 8b502966317c5d07e89de4ae70ff72b899e963e6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82084838"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Gyors útmutató: Azure Blockchain Service Consortium-hálózathoz való kapcsolódás a Visual Studio Code használatával

Ebben a rövid útmutatóban a Ethereum Visual Studio Code (VS Code) bővítményhez készült Azure Blockchain Development Kit használatával telepítheti és használhatja az Azure Blockchain szolgáltatásban található konzorciumhoz való csatolást. Az Azure Blockchain Development Kit leegyszerűsíti a Ethereum Blockchain-főkönyvekben található intelligens szerződések létrehozását, összekapcsolását, létrehozását és üzembe helyezését.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Teljes rövid útmutató [: blockchain-tag létrehozása a Azure Portal vagy a](create-member.md) gyors útmutató [: Azure blockchain Service Blockchain-tag létrehozása az Azure CLI használatával](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Az Azure Blockchain Development Kit for Ethereum bővítmény](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node. js 10.15. x vagy újabb](https://nodejs.org)
* [Git 2.10. x vagy újabb](https://git-scm.com)
* [Python-2.7.15](https://www.python.org/downloads/release/python-2715/) Adja hozzá a Python. exe fájlt az elérési úthoz. Ahhoz, hogy a Python-verzió 2.7.15 az Ön útvonalán, az Azure Blockchain Development Kit-nek kell lennie.
* [Szarvasgomba 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI-6.0.0](https://github.com/trufflesuite/ganache-cli)

Windows rendszeren a Node-GYP modulhoz egy telepített C++ fordító szükséges. Használhatja az MSBuild-eszközöket:

* Ha a Visual Studio 2017 telepítve van, konfigurálja a NPM az MSBuild-eszközök használatára a paranccsal`npm config set msvs_version 2017 -g`
* Ha a Visual Studio 2019 telepítve van, állítsa be az MS Build Tools elérési útját a NPM. Például: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Ellenkező esetben telepítse a különálló, vagy Build eszközöket egy emelt szintű `npm install --global windows-build-tools` *Futtatás rendszergazdaként* parancs-rendszerhéj használatával.

További információ a Node-GYP-ről: [Node-GYP adattár a githubon](https://github.com/nodejs/node-gyp).

### <a name="verify-azure-blockchain-development-kit-environment"></a>Az Azure Blockchain Development Kit-környezet ellenőrzése

Az Azure Blockchain Development Kit ellenőrzi, hogy teljesülnek-e a fejlesztési környezet előfeltételei. A fejlesztési környezet ellenőrzése:

A VS Code parancs palettáján válassza az **Azure Blockchain: Kezdőlap megjelenítése**lehetőséget.

Az Azure Blockchain Development Kit olyan érvényesítési parancsfájlt futtat, amely körülbelül egy percet vesz igénybe. Megtekintheti a kimenetet a **terminal > új terminál**lehetőség kiválasztásával. A terminál menüsorában válassza a **kimenet** fület és az **Azure Blockchain** a legördülő menüben. A sikeres érvényesítés az alábbi képhez hasonlóan néz ki:

![Érvényes fejlesztési környezet](./media/connect-vscode/valid-environment.png)

 Ha hiányzik egy szükséges eszköz, az **Azure Blockchain Development Kit** nevű új lap tartalmazza a letöltési hivatkozásokkal rendelkező szükséges eszközöket.

![Fejlesztői csomaghoz szükséges alkalmazások](./media/connect-vscode/required-apps.png)

A rövid útmutató folytatása előtt telepítse a hiányzó előfeltételeket.

## <a name="connect-to-consortium-member"></a>Kapcsolódás a konzorcium tagjához

A konzorcium tagjaihoz az Azure Blockchain Development Kit VS Code bővítmény használatával csatlakozhat. A konzorciumhoz való csatlakozás után intelligens szerződéseket állíthat össze, építhet ki és helyezhet üzembe egy Azure Blockchain Service Consortium-tag számára.

Ha nincs hozzáférése az Azure Blockchain Service Consortium egyik tagjához, hajtsa végre az előfeltételként szükséges rövid útmutatót [: hozzon létre egy Blockchain-tagot a Azure Portal vagy a](create-member.md) rövid útmutató [: Azure Blockchain Service Blockchain-tag létrehozása az Azure CLI használatával](create-member-cli.md).

1. A VS Code Explorer ablaktáblán bontsa ki az **Azure Blockchain** bővítményt.
1. Válassza **a kapcsolódás hálózathoz**lehetőséget.

   ![Kapcsolódás a hálózathoz](./media/connect-vscode/connect-consortium.png)

    Ha az Azure-hitelesítésre kéri, kövesse az utasításokat a böngészőben való hitelesítéshez.
1. Válassza ki az **Azure Blockchain szolgáltatást** a parancs-paletta legördülő menüben.
1. Válassza ki az Azure Blockchain Service Consortium-taghoz társított előfizetést és erőforráscsoportot.
1. Válassza ki a konzorciumot a listából.

A konzorcium és a blockchain tagjai a VS Code Explorer oldalsó sávján jelennek meg.

![A konzorcium megjelenik az Explorerben](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a Ethereum VS Code bővítményhez készült Azure Blockchain Development Kit használta az Azure Blockchain Service-konzorciumhoz való csatlakoztatáshoz. Próbálja ki a következő oktatóanyagot a Ethereum készült Azure Blockchain Development Kit használatával egy intelligens szerződési funkció létrehozásához, összeállításához, üzembe helyezéséhez és végrehajtásához tranzakción keresztül.

> [!div class="nextstepaction"]
> [Intelligens szerződések létrehozása, készítése és üzembe helyezése az Azure Blockchain Service-ben](send-transaction.md)