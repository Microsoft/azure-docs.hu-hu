---
title: Intelligens szerződések létrehozása, készítése, & üzembe helyezése oktatóanyag – Azure Blockchain szolgáltatás
description: Útmutató a Visual Studio Code-ban az Azure Blockchain Development Kit for Ethereum bővítmény használatáról az Azure Blockchain Service-ben intelligens szerződések létrehozásához, összeállításához és üzembe helyezéséhez.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: f7605a0c118a40e52210582d2411569795fb25ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763689"
---
# <a name="tutorial-create-build-and-deploy-smart-contracts-on-azure-blockchain-service"></a>Oktatóanyag: intelligens szerződések létrehozása, készítése és üzembe helyezése az Azure Blockchain Service-ben

Ebben az oktatóanyagban a Visual Studio Code-ban az Azure Blockchain Development Kit for Ethereum bővítmény használatával hozhat létre, építhet és helyezhet üzembe intelligens szerződést az Azure Blockchain Service-ben. A fejlesztői készlettel egy intelligens szerződési funkciót is végrehajthat tranzakción keresztül.

A Ethereum készült Azure Blockchain Development Kit a következő célokra használható:

> [!div class="checklist"]
> * Intelligens szerződés létrehozása
> * Intelligens szerződés üzembe helyezése
> * Intelligens szerződési funkció végrehajtása tranzakción keresztül

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Gyors útmutató [: a Visual Studio Code használata Azure Blockchain Service Consortium-hálózathoz való kapcsolódáshoz](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Az Azure Blockchain Development Kit for Ethereum bővítmény](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15. x vagy újabb](https://nodejs.org/download)
* [Git 2.10. x vagy újabb](https://git-scm.com)
* [Szarvasgomba 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI-6.0.0](https://github.com/trufflesuite/ganache-cli)

Windows rendszeren a Node-GYP modulhoz egy telepített C++ fordító szükséges. Használhatja az MSBuild-eszközöket:

* Ha a Visual Studio 2017 telepítve van, konfigurálja a NPM az MSBuild-eszközök használatára a paranccsal `npm config set msvs_version 2017 -g`
* Ha a Visual Studio 2019 telepítve van, állítsa be az MS Build Tools elérési útját a NPM. Például: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Ellenkező esetben telepítse a különálló, vagy Build eszközöket `npm install --global windows-build-tools` egy emelt szintű *Futtatás rendszergazdaként* parancs-rendszerhéj használatával.

További információ a Node-GYP-ről: [Node-GYP adattár a githubon](https://github.com/nodejs/node-gyp).

## <a name="create-a-smart-contract"></a>Intelligens szerződés létrehozása

A Ethereum készült Azure Blockchain Development Kit a Project templates és a szarvasgomba eszközt használja a szerződések készítéséhez, elkészítéséhez és üzembe helyezéséhez. Mielőtt elkezdené, hajtsa végre az előfeltételként szükséges útmutatót [: a Visual Studio Code használatával csatlakozhat egy Azure Blockchain Service Consortium-hálózathoz](connect-vscode.md). A rövid útmutató végigvezeti a Ethereum készült Azure Blockchain Development Kit telepítésének és konfigurálásának lépésein.

1. A VS Code parancs palettáján válassza a **Blockchain: új szilárdtest-projekt** elemet.
1. Válassza az **alapszintű projekt létrehozása** lehetőséget.
1. Hozzon létre egy nevű új mappát `HelloBlockchain` , és **válassza az új projekt elérési útja lehetőséget**.

Az Azure Blockchain Development Kit létrehoz és inicializál egy új szilárdtest-projektet. Az alapszintű projekt tartalmaz egy minta **HelloBlockchain** intelligens szerződést, valamint az összes szükséges fájlt, amely az Azure Blockchain szolgáltatásban a konzorciumi tag felépítésére és üzembe helyezésére szolgál. A projekt létrehozása több percet is igénybe vehet. Az Azure Blockchain kimenetének kiválasztásával nyomon követheti a VS Code termináljának előrehaladását.

A projekt szerkezete a következő példához hasonlóan néz ki:

   ![Szilárdtest-projekt](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Intelligens szerződés készítése

Az intelligens szerződések a projekt **szerződések** könyvtárában találhatók. Az intelligens szerződések fordítása előtt üzembe helyezheti azokat egy blockchain. A **szerződések összeállítása** paranccsal lefordíthatja a projektben lévő összes intelligens szerződést.

1. A VS Code Explorer oldalsávján bontsa ki a projekt **szerződések** mappáját.
1. Kattintson a jobb gombbal a **HelloBlockchain. Sol** elemre, és válassza a menü **szerződések létrehozása** elemét.

    ![Válassza ki a szerződések létrehozása menüt ](./media/send-transaction/build-contracts.png)

Az Azure Blockchain Development Kit a szarvasgomba használatával fordítja le az intelligens szerződéseket.

![Szarvasgomba fordítóprogram kimenete](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Intelligens szerződés üzembe helyezése

A szarvasgomba áttelepítési parancsfájlok használatával helyezi üzembe a szerződéseit egy Ethereum-hálózaton. A Migrálás a projekt **áttelepítési** könyvtárában található JavaScript-fájlok.

1. Az intelligens szerződés üzembe helyezéséhez kattintson a jobb gombbal a **HelloBlockchain. Sol** elemre, és válassza a menü **szerződések telepítése** menüpontját.
1. Válassza ki az Azure Blockchain Consortium-hálózatot a parancs palettáján. A projekt létrehozásakor a konzorcium blockchain-hálózata hozzá lett adva a projekt szarvasgomba konfigurációs fájljához.
1. Válassza a **hívóbetűje előállítása** lehetőséget. Válasszon egy fájlnevet, és mentse a hívóbetűje fájlt a projekt mappájába. Például: `myblockchainmember.env`. A hívóbetűje-fájl egy Ethereum titkos kulcs létrehozásához használható a blockchain-tag számára.

Az Azure Blockchain Development Kit a szarvasgomba használatával hajtja végre az áttelepítési parancsfájlt a szerződések a Blockchain való üzembe helyezéséhez.

![A szerződés sikeresen telepítve](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Szerződési függvény meghívása

A **HelloBlockchain** -szerződés **SendRequest hívás** funkciója módosítja a **RequestMessage** állapot változót. Egy blockchain-hálózat állapotának módosítása tranzakción keresztül történik. Az Azure Blockchain Development Kit intelligens szerződésének interakciós lapján meghívhatja a **SendRequest hívás** függvényt egy tranzakción keresztül.

1. Ha az intelligens szerződést szeretné használni, kattintson a jobb gombbal a **HelloBlockchain. Sol** elemre, és válassza az **intelligens szerződési interakció megjelenítése lapot** a menüből.

    ![Válassza az intelligens szerződési interakció megjelenítése lapot a menüből](./media/send-transaction/contract-interaction.png)

1. A kapcsolatitevékenység-oldal segítségével kiválaszthatja a központilag telepített szerződések verzióját, a hívási függvényeket, a jelenlegi állapotot, és megtekintheti a metaadatokat.

    ![Példa intelligens szerződés interakciós oldalára](./media/send-transaction/interaction-page.png)

1. Az intelligens szerződés függvény meghívásához válassza ki a szerződés műveletet, és adja át az argumentumait. Válassza a **SendRequest hívás** -szerződés művelet lehetőséget, és adja meg **a Hello, Blockchain!** a **requestMessage** paraméterhez. Válassza a **végrehajtás** lehetőséget a **SendRequest hívás** függvény tranzakción keresztüli meghívásához.

    ![SendRequest hívás művelet végrehajtása](./media/send-transaction/sendrequest-action.png)

A tranzakció feldolgozása után a interakció szakasz az állapot változásait tükrözi.

![Szerződések állapotának változásai](./media/send-transaction/contract-state.png)

A SendRequest hívás függvény a **RequestMessage** és az **állapot** mezőket állítja be. A **RequestMessage** aktuális állapota a **Hello, Blockchain** argumentum. Az **állapot** mező értéke továbbra is **kérés**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az erőforrásokat úgy, hogy törli a `myResourceGroup` *blockchain-tag* előfeltételeinek létrehozása előfeltételként létrehozott erőforráscsoportot.

Az erőforráscsoport törlése:

1. A Azure Portalban navigáljon az **erőforráscsoporthoz** a bal oldali navigációs ablaktáblán, és válassza ki a törölni kívánt erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzéséhez írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy minta-szilárdtest projektet az Azure Blockchain Development Kit használatával. Az Azure Blockchain Service-ben üzemeltetett blockchain Consortium-hálózat tranzakcióján keresztül létrehozott és telepített egy intelligens szerződést.

> [!div class="nextstepaction"]
> [Blockchain-alkalmazások fejlesztése az Azure Blockchain Service használatával](develop.md)
