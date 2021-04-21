---
title: Új Azure Blockchain Service létrehozása – Azure CLI
description: Hozzon létre egy Azure Blockchain Service egy blokklánc-konzorciumhoz az Azure CLI használatával.
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 2d94640e1c6a037893fb1ad9198b82195c46dd24
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768124"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Rövid útmutató: Blokklánctag Azure Blockchain Service létrehozása az Azure CLI használatával

Ebben a rövid útmutatóban egy új blokklánctagot és konzorciumot helyez üzembe a Azure Blockchain Service Azure CLI használatával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Nincsenek.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A böngészőablakot Cloud Shell böngészőlapon is elindíthatja a következő gombra: [https://shell.azure.com/bash](https://shell.azure.com/bash) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha inkább helyileg szeretné telepíteni és használni a CLI-t, ehhez a rövid útmutatóhoz az Azure CLI 2.0.51-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissítenie kell, tekintse meg az [Azure CLI telepítését.](/cli/azure/install-azure-cli)

## <a name="prepare-your-environment"></a>A környezet előkészítése

1. Jelentkezzen be.

    Jelentkezzen be az [az login](/cli/azure/reference-index#az_login) paranccsal, ha a CLI helyi telepítését használja.

    ```azurecli
    az login
    ```

    A terminálon megjelenő utasítások alapján végezze el a hitelesítési folyamatot.

1. Telepítse az Azure CLI-bővítményt.

    Ha az Azure CLI bővítményhivatkozásaival dolgozik, először telepítenie kell a bővítményt.  Az Azure CLI-bővítmények hozzáférést biztosítanak azokhoz a kísérleti és kiadás előtti parancsokhoz, amelyek az alap CLI-vel még nincsenek szállítva.  A bővítményekről, beleértve azok frissítését és eltávolítását is, a [Bővítmények használata az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview) című cikkben olvashat.

    Telepítse [a bővítményt a Azure Blockchain Service](/cli/azure/ext/blockchain/blockchain) a következő parancs futtatásával:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. Hozzon létre egy erőforráscsoportot.

    Azure Blockchain Service, mint minden Azure-erőforrást, egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

    Ebben a rövid útmutatóban hozzon létre egy _myResourceGroup_ nevű erőforráscsoportot az _eastus_ helyen a következő [az group create paranccsal:](/cli/azure/group#az_group_create)

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>Blokklánctag létrehozása

A Azure Blockchain Service tagja egy blokklánccsomópont egy privát konzorciumi blokklánchálózatban. Tag kiépítésekor konzorciumi hálózatot hozhat létre vagy csatlakozhat hozzá. Egy konzorciumhálózatnak legalább egy tagra van szüksége. A résztvevők számára szükséges blokklánctagok száma a forgatókönyvtől függ. A konzorciumi résztvevők egy vagy több blokklánctaggal is lehetnek, vagy megoszthatnak tagokat más résztvevőkkel. További információ a consortia projektről: Azure Blockchain Service [konzorcium.](consortium.md)

Számos paramétert és tulajdonságot kell átadnia. Cserélje le a példaparamétereket a saját értékeire.

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Az erőforráscsoport neve, Azure Blockchain Service létrejönnek az erőforrások. Használja az előző szakaszban létrehozott erőforráscsoportot.
| **név** | A blokklánctag nevét Azure Blockchain Service egyedi név. A nyilvános végpont címe a név. Például: `myblockchainmember.blockchain.azure.com`.
| **Helyen** | Az Azure-régió, ahol a blokklánctag létrejön. Például: `westus2`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet. Előfordulhat, hogy egyes régiókban a funkciók nem érhetők el. Azure Blockchain Data Manager azure-régiókban érhető el: az USA keleti régiója és Nyugat-Európa.
| **alaphelyzetbe állítása** | A tag alapértelmezett tranzakciós csomópontjának jelszava. Az alapszintű hitelesítéshez használja a jelszót, amikor a blokklánctag alapértelmezett tranzakciós csomópontjának nyilvános végpontjára csatlakozik.
| **Protokoll** | Blokklánc protokoll. A kvórum *protokoll jelenleg* támogatott.
| **Konzorcium** | A csatlakozáshoz vagy létrehozáshoz használt konzorcium neve. További információ a konzorciumról: Azure Blockchain Service [konzorcium.](consortium.md)
| **consortium-management-account-password** | A konzorciumfiók jelszavát tagfiókjelszónak is nevezik. A tagfiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására használatos. A konzorciumkezeléshez a tagfiókot és a tagfiók jelszavát használhatja.
| **Sku** | Réteg típusa. *Standard* vagy *Alapszintű.* Az *Alapszintű csomag* fejlesztési, tesztelési és koncepció igazolási célokra használható. Éles *környezetben való* üzembe helyezéshez használja a Standard szintet. Akkor is használja a *Standard* szintet, ha Blockchain Data Manager nagy mennyiségű privát tranzakciót küld. A tarifacsomag alapszintű és standard közötti módosítása a tag létrehozása után nem támogatott.

A blokklánctag és a támogató erőforrások létrehozása körülbelül 10 percet vesz igénybe.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használhatja a következő rövid útmutatóhoz vagy oktatóanyaghoz létrehozott blokklánctagot. Ha már nincs rá szükség, törölheti az erőforrásokat a rövid útmutatóhoz `myResourceGroup` létrehozott erőforráscsoport törlésével.

Az alábbi parancs futtatásával távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Azure Blockchain Service egy új konzorciumot. Próbálja ki a következő rövid útmutatót, hogy az Ethereumhoz Azure Blockchain fejlesztői készlettel csatoljon egy Azure Blockchain Service taghoz.

> [!div class="nextstepaction"]
> [Csatlakozás Visual Studio kód használatával Azure Blockchain Service](connect-vscode.md)
