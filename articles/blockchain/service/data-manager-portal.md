---
title: Blockchain-Data Manager konfigurálása Azure Portal-Azure Blockchain Service használatával
description: Blockchain-Data Manager létrehozása és kezelése az Azure Blockchain Service-hez a Azure Portal használatával.
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: a151c28d31bf0bb7f21185fb161315d42f9563d8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85200681"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>A Blockchain Data Manager konfigurálása az Azure Portallal

Konfigurálja a Blockchain Data Manager az Azure Blockchain szolgáltatáshoz, hogy rögzítse a Blockchain-adatgyűjtést, és küldje el egy Azure Event Grid témakörbe.

A Blockchain Data Manager példányának konfigurálásához a következőket kell tennie:

* Blockchain-Data Manager példány létrehozása Azure Blockchain szolgáltatásbeli tranzakciós csomóponthoz
* Blockchain-alkalmazások hozzáadása

## <a name="prerequisites"></a>Előfeltételek

* Teljes rövid útmutató [: blockchain-tag létrehozása a Azure Portal vagy a](create-member.md) gyors útmutató [: Azure blockchain Service Blockchain-tag létrehozása az Azure CLI használatával](create-member-cli.md). Az Azure Blockchain Service *standard* szintű Blockchain Data Manager használata esetén ajánlott.
* [Event Grid témakör](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) létrehozása
* További információ a [Azure Event Grid-beli eseménykezelőről](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Példány létrehozása

Egy Blockchain Data Manager-példány csatlakozik egy Azure Blockchain szolgáltatásbeli tranzakciós csomóponthoz, és figyeli azokat. Csak a tranzakciós csomóponthoz hozzáféréssel rendelkező felhasználók hozhatnak létre kapcsolatot. Egy példány az összes nyers blokk és nyers tranzakció adatait rögzíti a tranzakciós csomópontról. A Blockchain Data Manager közzétesz egy **RawBlockAndTransactionMsg** -üzenetet, amely a web3. ETH [GetBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) és a [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) lekérdezések által visszaadott információk egy részét képezi.

A kimenő kapcsolatok a blockchain adatokat küldenek Azure Event Grid. A példány létrehozásakor egyetlen kimenő kapcsolódást kell konfigurálnia. A Blockchain Data Manager több Event Grid témakör kimenő kapcsolatait is támogatja bármely adott Blockchain Data Manager-példány esetében. Blockchain-adatküldést egyetlen célhelyre is küldhet, vagy több célhelyre is küldhet blockchain-fájlokat. Egy másik cél hozzáadásához csak további kimenő kapcsolatokat adjon hozzá a példányhoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az Azure Blockchain szolgáltatás azon tagját, amelyhez csatlakozni szeretne a Blockchain Data Manager. Válassza ki a **Blockchain Data Manager**.
1. Válassza a **Hozzáadás** lehetőséget.

    ![Blockchain hozzáadása Data Manager](./media/data-manager-portal/add-instance.png)

    Adja meg a következő részleteket:

    Beállítás | Leírás
    --------|------------
    Név | Adjon egyedi nevet a csatlakoztatott Blockchain Data Manager. A Blockchain Data Manager neve kisbetűket és számokat tartalmazhat, és legfeljebb 20 karakter hosszúságú lehet.
    Tranzakciós csomópont | Válasszon tranzakciós csomópontot. Csak az olvasási hozzáféréssel rendelkező tranzakciós csomópontok jelennek meg.
    Kapcsolat neve | Adja meg a kimenő kapcsolatok egyedi nevét, ahol a blockchain tranzakciós adatküldés történik.
    Event Grid-végpont | Válassza ki az Event Grid-témakört ugyanabban az előfizetésben, mint a Blockchain Data Manager-példányt.

1. Válassza az **OK** lehetőséget.

    Egy Blockchain Data Manager példány létrehozásához kevesebb mint egy percet vesz igénybe. A példány üzembe helyezése után a rendszer automatikusan elindítja azt. Egy futó Blockchain Data Manager példány rögzíti a Blockchain eseményeket a tranzakciós csomópontból, és adatokat küld a kimenő kapcsolatoknak.

    Az új példány megjelenik a Blockchain Data Manager példányok listájában az Azure Blockchain szolgáltatás tagja számára.

    ![A Blockchain-adattagok példányainak listája](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Blockchain-alkalmazás hozzáadása

Blockchain-alkalmazás hozzáadásakor a Blockchain Data Manager dekódolja az alkalmazás esemény-és tulajdonsági állapotát. Ellenkező esetben a rendszer csak a nyers blokkot és a nyers tranzakciós adatkészletet küldi el. A Blockchain Data Manager a szerződések központi telepítésekor is felfedi a szerződések címét. Több blockchain-alkalmazást is hozzáadhat egy Blockchain Data Manager-példányhoz.

> [!IMPORTANT]
> Jelenleg a [blockchain vagy a](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) [leképezési típusokat](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) deklaráló alkalmazások nem teljes mértékben támogatottak. A tömbként vagy leképezési típusként deklarált tulajdonságokat a rendszer nem dekódolja a *ContractPropertiesMsg* vagy a *DecodedContractEventsMsg* üzenetekben.

Az Blockchain Data Manager az alkalmazás hozzáadásához intelligens szerződéssel rendelkező ABI-t és telepített bytecode-fájlt igényel.

### <a name="get-contract-abi-and-bytecode"></a>Egyezményes ABI és bytecode beszerzése

A szerződési ABI definiálja az intelligens szerződési felületeket. Leírja, hogyan kezelheti az intelligens szerződést. A [Ethereum-bővítményhez készült Azure Blockchain Development Kit](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) használatával másolhatja át a szerződéses ABI-t a vágólapra.

1. A Visual Studio Code Explorer ablaktáblán bontsa ki a szilárdtest-projekt **Build/szerződések** mappáját.
1. Kattintson a jobb gombbal a szerződés metaadatainak JSON-fájljára. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Válassza a **Szerződés másolása ABI** elemet.

    ![A Visual Studio Code panel és a másolási szerződés ABI kiválasztása](./media/data-manager-portal/abi-devkit.png)

    A rendszer átmásolja a szerződéses ABI-t a vágólapra.

1. Mentse az **ABI** TÖMBÖt JSON-fájlként. *abi.js* például:. Ezt a fájlt egy későbbi lépésben használhatja.

A Blockchain Data Manager igényli az intelligens szerződés üzembe helyezett bytecode. Az üzembe helyezett bytecode eltér az intelligens szerződés bytecode. Az Azure blockchain Development Kit bővítmény használatával másolja a bytecode a vágólapra.

1. A Visual Studio Code Explorer ablaktáblán bontsa ki a szilárdtest-projekt **Build/szerződések** mappáját.
1. Kattintson a jobb gombbal a szerződés metaadatainak JSON-fájljára. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Válassza a **tranzakció másolása bytecode** lehetőséget.

    ![Visual Studio Code ablaktábla a másolási tranzakció bytecode kijelölésével](./media/data-manager-portal/bytecode-devkit.png)

    A rendszer a vágólapra másolja a bytecode.

1. Mentse a **bytecode** -értéket JSON-fájlként. *bytecode.js* például:. Ezt a fájlt egy későbbi lépésben használhatja.

Az alábbi példa a VS Code Editorban megnyitott fájlok *abi.js* és *bytecode.jsét* mutatja be. A fájloknak hasonlóan kell kinézniük.

![Példa a fájlokra abi.jsés bytecode.js](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Egyezményes ABI és bytecode URL-cím létrehozása

A Blockchain Data Manager megköveteli, hogy a szerződési ABI és a bytecode fájlok elérhetők legyenek egy URL-cím használatával az alkalmazások hozzáadásakor. Az Azure Storage-fiókkal privát elérésű URL-címet is megadhat.

#### <a name="create-storage-account"></a>Storage-fiók létrehozása

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Szerződések fájljainak feltöltése

1. Hozzon létre egy új tárolót a Storage-fiókhoz. Válassza a tárolók **> tároló** elemet.

    ![Storage-fiók tárolójának létrehozása](./media/data-manager-portal/create-container.png)

    | Mező | Leírás |
    |-------|-------------|
    | Név  | Nevezze el a tárolót. Például: *smartcontract* |
    | Nyilvános hozzáférés szintje | *Privát kiválasztása (névtelen hozzáférés nélkül)* |

1. A tároló létrehozásához válassza az **OK** gombot.
1. Válassza ki a tárolót, majd válassza a **feltöltés** lehetőséget.
1. Válassza ki a [szerződések beszerzése ABI és bytecode](#get-contract-abi-and-bytecode) szakaszban létrehozott JSON-fájlokat.

    ![BLOB feltöltése](./media/data-manager-portal/upload-blobs.png)

    Válassza a **Feltöltés** lehetőséget.

#### <a name="generate-url"></a>URL-cím előállítása

Az egyes Blobok esetében egy közös hozzáférési aláírás létrehozása.

1. Válassza ki az ABI JSON-blobot.
1. **Sas-előállítók** kiválasztása
1. Állítsa be a kívánt hozzáférési aláírás lejáratát, majd válassza **a blob sas-token és URL-cím létrehozása** lehetőséget.

    ![SAS-token előállítása](./media/data-manager-portal/generate-sas.png)

1. Másolja a **blob sas URL-címét** , és mentse a következő szakaszra.
1. Ismételje meg a bytecode JSON [-blob URL-címének előállítása](#generate-url) lépéseit.

### <a name="add-application-to-instance"></a>Alkalmazás hozzáadása a példányhoz

1. Válassza ki a Blockchain Data Manager példányt a példány listából.
1. Válassza a **Blockchain-alkalmazások** elemet.
1. Válassza a **Hozzáadás** lehetőséget.

    ![Blockchain-alkalmazás hozzáadása](./media/data-manager-portal/add-application.png)

    Adja meg a blockchain-alkalmazás nevét és az intelligens szerződési ABI és bytecode URL-eket.

    Beállítás | Leírás
    --------|------------
    Név | Adjon meg egy egyedi nevet a nyomon követett blockchain-alkalmazás számára.
    Egyezményes ABI | A szerződéses ABI-fájl URL-címének elérési útja. További információkért lásd: [egyezményes ABI és bytecode URL-cím létrehozása](#create-contract-abi-and-bytecode-url).
    Szerződés bytecode | A bytecode fájl URL-elérési útja. További információkért lásd: [egyezményes ABI és bytecode URL-cím létrehozása](#create-contract-abi-and-bytecode-url).

1. Válassza az **OK** lehetőséget.

    Az alkalmazás létrehozása után az alkalmazás megjelenik a blockchain-alkalmazások listájában.

    ![Blockchain-alkalmazások listája](./media/data-manager-portal/artifact-list.png)

Törölheti az Azure Storage-fiókot, vagy felhasználhatja további blockchain alkalmazások konfigurálásához. Ha törölni szeretné az Azure Storage-fiókot, törölheti az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó tárfiók, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

## <a name="stop-instance"></a>Példány leállítása

Állítsa le a Blockchain Manager-példányt, ha le szeretné állítani a Blockchain-események rögzítését és az adatok küldését a kimenő kapcsolatokra. Ha a példány leáll, a Blockchain Data Managerért nem számítunk fel díjat. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/blockchain-service) tartalmaz.

1. Lépjen az **Áttekintés** elemre, és válassza a **Leállítás** lehetőséget.

    ![Példány leállítása](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Következő lépések

Próbálja ki a következő oktatóanyagot, amely egy blockchain-tranzakciós Blockchain-kezelőt hoz létre a Data Manager és Azure Cosmos DB használatával.

> [!div class="nextstepaction"]
> [Adatok küldése az Azure Cosmos DB-be a Blockchain Data Managerrel](data-manager-cosmosdb.md)