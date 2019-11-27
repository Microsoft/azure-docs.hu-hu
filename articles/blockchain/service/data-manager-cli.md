---
title: Blockchain-Data Manager konfigurálása az Azure CLI-vel – Azure Blockchain Service
description: Azure Blockchain Service-hez készült Blockchain-Data Manager létrehozása és kezelése az Azure CLI-vel
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: a8061aad6d6a1513de70e7c2bc57aa109c666611
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455942"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>A Blockchain Data Manager konfigurálása az Azure CLI-vel

Az Azure Blockchain Service-hez készült Blockchain-Data Manager konfigurálása a Blockchain-adatrögzítés Azure Event Grid témakörbe való elküldéséhez.

A Blockchain Data Manager példányának konfigurálásához a következőket kell tennie:

* Blockchain Manager-példány létrehozása
* Bemenet létrehozása Azure Blockchain szolgáltatásbeli tranzakciós csomópontba
* Kimenet létrehozása Azure Event Grid témakörhöz
* Blockchain-alkalmazás hozzáadása
* Példány elindítása

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a legújabb [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) -t, és jelentkezzen be `az login`használatával.
* Gyors útmutató [: a Visual Studio Code használata Azure Blockchain Service Consortium-hálózathoz való kapcsolódáshoz](connect-vscode.md)
* [Event Grid témakör](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) létrehozása
* További információ a [Azure Event Grid-beli eseménykezelőről](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/bash](https://shell.azure.com/bash) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.51 vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli)ismertető témakört.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Példány létrehozása

Egy Blockchain Data Manager-példány figyeli az Azure Blockchain szolgáltatás tranzakciós csomópontját. Egy példány az összes nyers blokk és nyers tranzakció adatait rögzíti a tranzakciós csomópontról.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Paraméter | Leírás |
|-----------|-------------|
| resource-group | Az erőforráscsoport neve, ahol a Blockchain Data Manager példányt hozza létre. |
| név | A Blockchain Data Manager példányának neve. |
| Erőforrás típusa | A Blockchain Data Manager példányának erőforrástípus a **Microsoft. Blockchain/figyelők**. |
| is-full-object | Azt jelzi, hogy a tulajdonságok tartalmazzák-e a figyelők erőforrásának beállításait. |
| properties | A figyelő erőforráshoz tartozó tulajdonságokat tartalmazó JSON-formázott karakterlánc. Karakterláncként vagy fájlként adható át.  |

### <a name="create-instance-examples"></a>Példányok létrehozása – példák

JSON-konfiguráció – példa Blockchain Manager-példány létrehozására az **USA keleti** régiójában.

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Elem | Leírás |
|---------|-------------|
| location | Az a régió, ahol a figyelő erőforrást létre kell hozni |
| properties | A figyelők erőforrásának létrehozásakor beállítani kívánt tulajdonságok |

Hozzon létre egy *mywatcher* nevű Data Manager Blockchain-példányt egy JSON-karakterlánc használatával a konfigurációhoz.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Hozzon létre egy *mywatcher* nevű Data Manager Blockchain-példányt egy JSON-konfigurációs fájl használatával.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Hozzon létre bemeneti

Egy bemenet összekapcsolja az Blockchain Data Manager egy Azure Blockchain szolgáltatás-tranzakciós csomóponttal. Csak a tranzakciós csomóponthoz hozzáféréssel rendelkező felhasználók hozhatnak létre kapcsolatot.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Paraméter | Leírás |
|-----------|-------------|
| resource-group | Az erőforráscsoport neve, ahol a bemeneti erőforrást létre kell hozni. |
| név | A bemenet neve. |
| névtér | Használja a **Microsoft. Blockchain** szolgáltatói névteret. |
| Erőforrás típusa | A bemenetek Blockchain Data Manager input **típusú.** |
| szülő | Azon figyelő elérési útja, amelyhez a bemenet hozzá van rendelve. Például: **figyelők/mywatcher**. |
| is-full-object | Azt jelzi, hogy a tulajdonságok tartalmazzák-e a bemeneti erőforrás beállításait. |
| properties | A bemeneti erőforrás tulajdonságait tartalmazó JSON-formázott karakterlánc. Karakterláncként vagy fájlként adható át. |

### <a name="input-examples"></a>Bemeneti példák

Konfiguráció JSON-példa egy bemeneti erőforrás létrehozásához az *USA keleti* régiójában, amely \<Blockchain-tag\>hoz csatlakozik.

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Elem | Leírás |
|---------|-------------|
| location | Az a régió, ahol a bemeneti erőforrást létre kell hozni. |
| inputType | Az Azure Blockchain-szolgáltatás tagjának főkönyvi típusa. Jelenleg a **Ethereum** támogatott. |
| resourceId | A tranzakciós csomópont, amelyhez a bemenet csatlakozik. Cserélje le \<előfizetés-azonosító\>, \<erőforráscsoport\>és \<Blockchain-tag\> a tranzakciós csomópont erőforrásának értékeire. A bemenet az Azure Blockchain Service-tag alapértelmezett tranzakciós csomópontjára csatlakozik. |

Hozzon létre egy *myInput* for *mywatcher* nevű bemenetet egy JSON-karakterlánc használatával a konfiguráláshoz.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Hozzon létre egy *myInput* nevű bemenetet a *mywatcher* egy JSON-konfigurációs fájl használatával.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Kimenet létrehozása

A kimenő kapcsolatok a blockchain adatokat küldenek Azure Event Grid. Blockchain-adatküldést egyetlen célhelyre is küldhet, vagy több célhelyre is küldhet blockchain-fájlokat. A Blockchain Data Manager több Event Grid témakör kimenő kapcsolatait is támogatja bármely adott Blockchain Data Manager-példány esetében.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Paraméter | Leírás |
|-----------|-------------|
| resource-group | Az erőforráscsoport neve, ahol a kimeneti erőforrást létre kell hozni. |
| név | A kimenet neve. |
| névtér | Használja a **Microsoft. Blockchain** szolgáltatói névteret. |
| Erőforrás típusa | Egy Blockchain erőforrás-típusa Data Manager **kimenet.** |
| szülő | Azon figyelő elérési útja, amelyhez a kimenet társítva van. Például: **figyelők/mywatcher**. |
| is-full-object | Azt jelzi, hogy a tulajdonságok tartalmaznak-e beállításokat a kimeneti erőforráshoz. |
| properties | A kimeneti erőforrás tulajdonságait tartalmazó JSON formátumú karakterlánc. Karakterláncként vagy fájlként adható át. |

### <a name="output-examples"></a>Kimeneti példák

Konfigurációs JSON-példa egy kimeneti erőforrás létrehozásához az *USA keleti* régiójában, amely egy \<Event grid témakör\>nevű Event Grid-témakörhöz csatlakozik.

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Elem | Leírás |
|---------|-------------|
| location | Az a régió, ahol a kimeneti erőforrást létre kívánja hozni. |
| outputType | A kimenet típusa. Jelenleg a **EventGrid** támogatott. |
| resourceId | Az erőforrás, amelyhez a kimenet csatlakozik. Cserélje le \<előfizetés-azonosító\>, \<erőforráscsoport\>és \<Blockchain-tag\> az Event Grid-erőforrás értékeit. |

Hozzon létre egy *myoutput* nevű kimenetet a *mywatcher* , amely egy JSON-konfigurációs karakterlánc használatával csatlakozik egy Event Grid-témakörhöz.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

Hozzon létre egy *myoutput* nevű kimenetet a *mywatcher* , amely egy JSON-konfigurációs fájl használatával csatlakozik egy Event Grid-témakörhöz.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Blockchain-alkalmazás hozzáadása

Blockchain-alkalmazás hozzáadásakor a Blockchain Data Manager dekódolja az alkalmazás esemény-és tulajdonsági állapotát. Ellenkező esetben a rendszer csak a nyers blokkot és a nyers tranzakciós adatkészletet küldi el. A Blockchain Data Manager a szerződések központi telepítésekor is felfedi a szerződések címét. Több blockchain-alkalmazást is hozzáadhat egy Blockchain Data Manager-példányhoz.


> [!IMPORTANT]
> Jelenleg a [blockchain vagy a](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) [leképezési típusokat](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) deklaráló alkalmazások nem teljes mértékben támogatottak. A tömbként vagy leképezési típusként deklarált tulajdonságokat a rendszer nem dekódolja a *ContractPropertiesMsg* vagy a *DecodedContractEventsMsg* üzenetekben.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Paraméter | Leírás |
|-----------|-------------|
| resource-group | Az erőforráscsoport neve, ahol létre kell hozni az alkalmazás erőforrását. |
| név | Az alkalmazás neve. |
| névtér | Használja a **Microsoft. Blockchain** szolgáltatói névteret. |
| Erőforrás típusa | Egy Blockchain Data Manager alkalmazás erőforrás-típusa **összetevők.** |
| szülő | Azon figyelő elérési útja, amelyhez az alkalmazás társítva van. Például: **figyelők/mywatcher**. |
| is-full-object | Azt jelzi, hogy a tulajdonságok tartalmazzák-e az alkalmazás erőforrásának beállításait. |
| properties | A JSON-formátumú karakterlánc, amely az alkalmazás erőforrásának tulajdonságait tartalmazza. Karakterláncként vagy fájlként adható át. |

### <a name="blockchain-application-examples"></a>Példák a Blockchain alkalmazásra

Konfigurációs JSON-példa egy alkalmazás-erőforrás létrehozására az *USA keleti* régiójában, amely FIGYELI az ABI és a bytecode egyezmény által meghatározott intelligens szerződést.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Elem | Leírás |
|---------|-------------|
| location | A régió, ahol az alkalmazás-erőforrást létre kell hozni. |
| artifactType | Az alkalmazás típusa. Jelenleg a **EthereumSmartContract** támogatott. |
| abiFileUrl | Az intelligens szerződések ABI JSON-fájljának URL-címe. A szerződéses ABI beszerzésével és az URL-cím létrehozásával kapcsolatos további információkért lásd: az [ABI és a bytecode szerződés lekérése](data-manager-portal.md#get-contract-abi-and-bytecode) és a [szerződési ABI és bytecode URL-cím létrehozása](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | Az intelligens szerződés üzembe helyezett bytecode JSON-fájljának URL-címe. Az intelligens szerződések üzembe helyezett bytecode és URL-cím létrehozásával kapcsolatos további információkért lásd: az [ABI és a bytecode szerződés](data-manager-portal.md#get-contract-abi-and-bytecode) beszerzése és a [szerződési ABI és bytecode URL-cím létrehozása](data-manager-portal.md#create-contract-abi-and-bytecode-url). Megjegyzés: a Blockchain Data Manager igényli a **telepített bytecode**. |
| queryTargetTypes | Közzétett üzenetek típusai. A **ContractProperties** megadásával közzéteheti a *ContractPropertiesMsg* üzenet típusát. A **ContractEvents** megadásával közzéteheti a *DecodedContractEventsMsg* üzenet típusát. Megjegyzés: a *RawBlockAndTransactionMsg* és a *RawTransactionContractCreationMsg* típusú üzenetek mindig közzé lesznek téve. |

Hozzon létre egy *myApplication* for *mywatcher* nevű alkalmazást, amely figyeli egy JSON-karakterlánc által definiált intelligens szerződést.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Hozzon létre egy *myApplication* for *mywatcher* nevű alkalmazást, amely egy JSON-konfigurációs fájllal definiált intelligens szerződést figyel.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Példány indítása

A futtatásakor a Blockchain Manager-példány figyeli a megadott bemenetek Blockchain eseményeit, és adatokat küld a megadott kimeneteknek.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Paraméter | Leírás |
|-----------|-------------|
| action | A figyelő futtatásához használja a **Start** parancsot. |
| azonosítók | Figyelő erőforrás-azonosítója. Cserélje le \<előfizetés-azonosító\>, \<erőforráscsoport\>, és \<figyelő nevét\> a megfigyelő erőforrás értékeit.|

### <a name="start-instance-example"></a>Példány indítása példa

Indítsa el a *mywatcher*nevű Data Manager Blockchain-példányt.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Példány leállítása

Blockchain Data Manager példány leállítása.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Paraméter | Leírás |
|-----------|-------------|
| action | A figyelő leállításához használja a **Leállítás** lehetőséget. |
| azonosítók | A figyelő neve. Cserélje le \<előfizetés-azonosító\>, \<erőforráscsoport\>, és \<figyelő nevét\> a megfigyelő erőforrás értékeit. |

### <a name="stop-watcher-example"></a>Figyelő leállítása – példa

Állítson le egy *mywatcher*nevű példányt.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Példány törlése

Blockchain Data Manager példány törlése.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Paraméter | Leírás |
|-----------|-------------|
| resource-group | A törlendő figyelő erőforráscsoport-neve. |
| név | A törlendő figyelő neve. |
| Erőforrás típusa | Egy Blockchain Data Manager figyelő erőforrás **-típusa Microsoft. Blockchain/figyelők**. |

### <a name="delete-instance-example"></a>Példány törlése példa

Törölje a *mywatcher* nevű példányt a *myRG* erőforráscsoporthoz.

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Következő lépések

Próbálja ki a következő oktatóanyagot, amely egy blockchain-tranzakciós Blockchain-kezelőt hoz létre a Data Manager és Azure Cosmos DB használatával.

> [!div class="nextstepaction"]
> [Az Blockchain Data Manager használata az adatAzure Cosmos DBba való adatküldéshez](data-manager-cosmosdb.md)
