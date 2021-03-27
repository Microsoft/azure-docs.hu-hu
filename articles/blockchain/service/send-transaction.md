---
title: Intelligens szerződések létrehozása, készítése, & üzembe helyezése oktatóanyag – Azure Blockchain szolgáltatás
description: Útmutató a Visual Studio Code-ban az Azure Blockchain Development Kit for Ethereum bővítmény használatáról az Azure Blockchain Service-ben intelligens szerződések létrehozásához, összeállításához és üzembe helyezéséhez.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: 4c2df952480d2c30de10838c3d0f7714fc7e6126
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628645"
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
A **HelloBlockchain** -szerződés **SendRequest hívás** funkciója módosítja a **RequestMessage** állapot változót. Egy blockchain-hálózat állapotának módosítása tranzakción keresztül történik. Létrehozhat egy parancsfájlt, amely a **SendRequest hívás** függvényt tranzakción keresztül hajtja végre.

1. Hozzon létre egy új fájlt a szarvasgomba-projekt gyökerében, és nevezze el `sendrequest.js` . Adja hozzá a következő Web3 JavaScript-kódot a fájlhoz.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Ha az Azure Blockchain Development Kit létrehoz egy projektet, a rendszer létrehozza a szarvasgomba konfigurációs fájlt a konzorcium Blockchain hálózati végpontjának részleteivel. Nyissa meg **truffle-config.js** a projektben. A konfigurációs fájl két hálózatot listáz: egy névvel ellátott fejlesztést és egyet a konzorcium nevével.
1. A VS Code terminál ablaktábláján a szarvasgomba használatával hajtsa végre a szkriptet a konzorcium blockchain-hálózatán. A terminál ablaktáblájának menüsorában válassza a **terminál** fület és a **PowerShellt** a legördülő menüben.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Cserélje le a értéket a \<blockchain network\> **truffle-config.jsban** definiált blockchain-hálózat nevére.

A szarvasgomba végrehajtja a szkriptet a blockchain-hálózaton.

![A tranzakciót küldő kimenet](./media/send-transaction/execute-transaction.png)

Ha egy tranzakción keresztül hajtja végre a szerződés függvényét, a tranzakció addig nem lesz feldolgozva, amíg létre nem jön egy blokk. Az olyan függvények, amelyeket egy tranzakción keresztül kell végrehajtani, visszatérési érték helyett tranzakció-azonosítót adnak vissza.

## <a name="query-contract-state"></a>Szerződés állapotának lekérdezése

Az intelligens szerződési függvények az állapot változóinak aktuális értékét adhatják vissza. Adjon hozzá egy függvényt egy állapotjelző változó értékének visszaadásához.

1. A **HelloBlockchain. Sol**-ben adjon hozzá egy **getMessage** függvényt a **HelloBlockchain** intelligens szerződéshez.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    A függvény az állapot változójában tárolt üzenetet adja vissza a szerződés aktuális állapota alapján.

1. Kattintson a jobb gombbal a **HelloBlockchain. Sol** elemre, majd válassza a **szerződések létrehozása** lehetőséget a menüből az intelligens szerződés módosításainak fordításához.
1. A telepítéshez kattintson a jobb gombbal a **HelloBlockchain. Sol** elemre, és válassza a menü **szerződések telepítése** menüpontját. Ha a rendszer kéri, válassza ki az Azure Blockchain Consortium-hálózatot a parancs palettáján.
1. Ezután hozzon létre egy szkriptet a paranccsal a **getMessage** függvény meghívásához. Hozzon létre egy új fájlt a szarvasgomba-projekt gyökerében, és nevezze el `getmessage.js` . Adja hozzá a következő Web3 JavaScript-kódot a fájlhoz.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. A VS Code terminál ablaktábláján a szarvasgomba használatával hajtsa végre a szkriptet a blockchain-hálózaton. A terminál ablaktáblájának menüsorában válassza a **terminál** fület és a **PowerShellt** a legördülő menüben.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Cserélje le a értéket a \<blockchain network\> **truffle-config.jsban** definiált blockchain-hálózat nevére.

A szkript lekérdezi az intelligens szerződést a getMessage függvény meghívásával. A rendszer visszaadja a **RequestMessage** állapot változó aktuális értékét.

![A GetMessage-lekérdezés kimenete, amely a RequestMessage állapot változó aktuális értékét mutatja.](./media/send-transaction/execute-get.png)

Figyelje meg, hogy az érték nem **Hello, blockchain!**. Ehelyett a visszaadott érték helyőrző. Ha módosítja és telepíti a szerződést, a rendszer új címen telepíti a módosított szerződést, és az állapot változói az intelligens szerződés konstruktorában vannak hozzárendelve. A szarvasgomba minta **2_deploy_contracts.js** áttelepítési parancsfájl üzembe helyezi az intelligens szerződést, és argumentumként átadja a helyőrző értékét. A konstruktor a **RequestMessage** állapot változót a helyőrző értékre állítja, és a függvény a visszaadott értéket adja vissza.

1. A **RequestMessage** állapot változó beállításához és az érték lekérdezéséhez futtassa újra a **sendrequest.js** és **getmessage.js** szkripteket.

    ![SendRequest hívás-és GetMessage-parancsfájlok kimenete, amely a RequestMessage-t jeleníti meg](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** beállítja a **RequestMessage** állapot változót a Hello értékre **, blockchain!** és **getmessage.js** lekérdezi a szerződést a **RequestMessage** State változó értékére, és a következőt adja vissza: **Hello, blockchain!**.
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az erőforrásokat úgy, hogy törli a `myResourceGroup` *blockchain-tag* előfeltételeinek létrehozása előfeltételként létrehozott erőforráscsoportot.

Az erőforráscsoport törlése:

1. A Azure Portalban navigáljon az **erőforráscsoporthoz** a bal oldali navigációs ablaktáblán, és válassza ki a törölni kívánt erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzéséhez írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy minta-szilárdtest projektet az Azure Blockchain Development Kit használatával. Az Azure Blockchain Service-ben üzemeltetett blockchain Consortium-hálózat tranzakcióján keresztül létrehozott és telepített egy intelligens szerződést.

> [!div class="nextstepaction"]
> [Blockchain-alkalmazások fejlesztése az Azure Blockchain Service használatával](develop.md)
