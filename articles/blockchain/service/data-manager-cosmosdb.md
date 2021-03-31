---
title: A Blockchain Data Manager használata az Azure Cosmos DB-Azure Blockchain szolgáltatás frissítéséhez
description: Blockchain-Data Manager használata az Azure Blockchain Service-hez Blockchain-Azure Cosmos DB küldéséhez
ms.date: 03/08/2020
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 69790787bc888448f2f40178bd12ee7058cc5892
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91283453"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Oktatóanyag: az Blockchain-Data Manager használata az adatküldés Azure Cosmos DB

Ebben az oktatóanyagban az Azure Blockchain Service-hez készült Blockchain-Data Manager használatával rögzíti a Blockchain-tranzakciós adatok Azure Cosmos DB. A Blockchain Data Manager rögzíti, átalakítja és kézbesíti a Blockchain Ledger-információt Azure Event Grid témakörökbe. Azure Event Grid az Azure Logic app-összekötővel dokumentumokat hozhat létre egy Azure Cosmos DB-adatbázisban. Ha elkészült az Oktatóanyaggal, megtekintheti Azure Cosmos DB Adatkezelő blockchain tranzakciós szolgáltatásait.

[![A képernyőképen a blockchain tranzakció részletei láthatók.](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Blockchain-Data Manager példány létrehozása
> * Blockchain-alkalmazás hozzáadása a tranzakciók tulajdonságainak és eseményeinek dekódolásához
> * Azure Cosmos DB-fiók és-adatbázis létrehozása a tranzakciós adattároláshoz
> * Hozzon létre egy Azure logikai alkalmazást egy Azure Event Grid témakör Azure Cosmos DBhoz való összekapcsolásához
> * Tranzakció küldése blockchain-főkönyvbe
> * A dekódolású tranzakciós adatAzure Cosmos DB megtekintése

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Teljes rövid útmutató [: blockchain-tag létrehozása a Azure Portal vagy a](create-member.md) gyors útmutató [: Azure blockchain Service Blockchain-tag létrehozása az Azure CLI használatával](create-member-cli.md)
* Teljes rövid útmutató [: a Visual Studio Code használatával csatlakozhat egy Azure Blockchain Service Consortium-hálózathoz](connect-vscode.md). A rövid útmutató végigvezeti a [Ethereum készült Azure Blockchain Development Kit](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) telepítésének és a Blockchain-fejlesztési környezet beállításának lépésein.
* Teljes [oktatóanyag: intelligens szerződések létrehozása, készítése és üzembe helyezése a Visual Studio Code használatával](send-transaction.md). Az oktatóanyag végigvezeti a minta intelligens szerződés létrehozásán.
* [Event Grid témakör](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) létrehozása
* További információ a [Azure Event Grid-beli eseménykezelőről](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Példány létrehozása

Egy Blockchain Data Manager-példány csatlakozik egy Azure Blockchain szolgáltatásbeli tranzakciós csomóponthoz, és figyeli azokat. Egy példány az összes nyers blokk és nyers tranzakció adatait rögzíti a tranzakciós csomópontról. A kimenő kapcsolatok a blockchain adatokat küldenek Azure Event Grid. A példány létrehozásakor egyetlen kimenő kapcsolódást kell konfigurálnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az előfeltételként szolgáló rövid útmutatóban létrehozott Azure Blockchain Service-tagot [: hozzon létre egy Blockchain-tagot a Azure Portal használatával](create-member.md). Válassza ki a **Blockchain Data Manager**.
1. Válassza a **Hozzáadás** lehetőséget.

    ![Blockchain hozzáadása Data Manager](./media/data-manager-cosmosdb/add-instance.png)

    Adja meg a következő részleteket:

    Beállítás | Példa | Leírás
    --------|---------|------------
    Név | mywatcher | Adjon egyedi nevet a csatlakoztatott Blockchain Data Manager.
    Tranzakciós csomópont | myblockchainmember | Válassza ki az előfeltételként létrehozott Azure Blockchain Service-tag alapértelmezett tranzakciós csomópontját.
    Kapcsolat neve | cosmosdb | Adja meg a kimenő kapcsolatok egyedi nevét, ahol a blockchain tranzakciós adatküldés történik.
    Event Grid-végpont | myTopic | Válassza ki az előfeltételként létrehozott Event Grid-témakört. Megjegyzés: a Blockchain Data Manager példányának és az Event Grid témakörnek ugyanahhoz az előfizetéshez kell tartoznia.

1. Válassza az **OK** lehetőséget.

    Egy Blockchain Data Manager példány létrehozásához kevesebb mint egy percet vesz igénybe. A példány üzembe helyezése után a rendszer automatikusan elindítja azt. Egy futó Blockchain Data Manager példány rögzíti a Blockchain eseményeket a tranzakciós csomópontból, és adatokat küld az Event Gridnek.

## <a name="add-application"></a>Alkalmazás hozzáadása

Adja hozzá a **helloblockchain** blockchain alkalmazást, hogy a blockchain Data Manager dekódolja az esemény és a tulajdonság állapotát. A Blockchain Data Manager megköveteli az intelligens szerződési ABI és a bytecode fájl használatát az alkalmazás hozzáadásához.

### <a name="get-contract-abi-and-bytecode"></a>Egyezményes ABI és bytecode beszerzése

A szerződési ABI definiálja az intelligens szerződési felületeket. Leírja, hogyan kezelheti az intelligens szerződést. A [Ethereum-bővítményhez készült Azure Blockchain Development Kit](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) használatával másolhatja át a szerződéses ABI-t a vágólapra.

1. A Visual Studio Code Explorer ablaktáblán bontsa ki az előfeltételként szolgáló oktatóanyagban létrehozott **helloblockchain** -szilárdtest projekt **Build/szerződések** mappáját [: a Visual Studio Code használatával intelligens szerződéseket hozhat létre, építhet ki és helyezhet üzembe](send-transaction.md).
1. Kattintson a jobb gombbal a szerződés metaadatainak JSON-fájljára. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Válassza a **Szerződés másolása ABI** elemet.

    ![A Visual Studio Code panel és a másolási szerződés ABI kiválasztása](./media/data-manager-cosmosdb/abi-devkit.png)

    A rendszer átmásolja a szerződéses ABI-t a vágólapra.

1. Mentse az **ABI** TÖMBÖt JSON-fájlként. *abi.js* például:. Ezt a fájlt egy későbbi lépésben használhatja.

A Blockchain Data Manager igényli az intelligens szerződés üzembe helyezett bytecode. Az üzembe helyezett bytecode eltér az intelligens szerződés bytecode. Az Azure blockchain Development Kit bővítmény használatával másolja a bytecode a vágólapra.

1. A Visual Studio Code Explorer ablaktáblán bontsa ki a szilárdtest-projekt **Build/szerződések** mappáját.
1. Kattintson a jobb gombbal a szerződés metaadatainak JSON-fájljára. A fájl neve az intelligens szerződés neve, amelyet a **. JSON** kiterjesztés követ.
1. Válassza a **tranzakció másolása bytecode** lehetőséget.

    ![Visual Studio Code ablaktábla a másolási tranzakció bytecode kijelölésével](./media/data-manager-cosmosdb/bytecode-devkit.png)

    A rendszer a vágólapra másolja a bytecode.

1. Mentse a **bytecode** -értéket JSON-fájlként. *bytecode.js* például:. Ezt a fájlt egy későbbi lépésben használhatja.

Az alábbi példa a VS Code Editorban megnyitott fájlok *abi.js* és *bytecode.jsét* mutatja be. A fájloknak hasonlóan kell kinézniük.

![Példa a fájlokra abi.jsés bytecode.js](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Egyezményes ABI és bytecode URL-cím létrehozása

A Blockchain Data Manager megköveteli, hogy a szerződési ABI és a bytecode fájlok elérhetők legyenek egy URL-cím használatával az alkalmazások hozzáadásakor. Az Azure Storage-fiókkal privát elérésű URL-címet is megadhat.

#### <a name="create-storage-account"></a>Storage-fiók létrehozása

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Szerződések fájljainak feltöltése

1. Hozzon létre egy új tárolót a Storage-fiókhoz. Válassza a tárolók **> tároló** elemet.

    ![Storage-fiók tárolójának létrehozása](./media/data-manager-cosmosdb/create-container.png)

    | Beállítás | Leírás |
    |---------|-------------|
    | Név  | Nevezze el a tárolót. Például: *smartcontract* |
    | Nyilvános hozzáférés szintje | *Privát kiválasztása (névtelen hozzáférés nélkül)* |

1. A tároló létrehozásához válassza az **OK** gombot.
1. Válassza ki a tárolót, majd válassza a **feltöltés** lehetőséget.
1. Válassza ki a [szerződések beszerzése ABI és bytecode](#get-contract-abi-and-bytecode) szakaszban létrehozott JSON-fájlokat.

    ![BLOB feltöltése](./media/data-manager-cosmosdb/upload-blobs.png)

    Válassza a **Feltöltés** lehetőséget.

#### <a name="generate-url"></a>URL-cím előállítása

Az egyes Blobok esetében egy közös hozzáférési aláírás létrehozása.

1. Válassza ki az ABI JSON-blobot.
1. **Sas-előállítók** kiválasztása
1. Állítsa be a kívánt hozzáférési aláírás lejáratát, majd válassza **a blob sas-token és URL-cím létrehozása** lehetőséget.

    ![SAS-token előállítása](./media/data-manager-cosmosdb/generate-sas.png)

1. Másolja a **blob sas URL-címét** , és mentse a következő szakaszra.
1. Ismételje meg a bytecode JSON [-blob URL-címének előállítása](#generate-url) lépéseit.

### <a name="add-helloblockchain-application-to-instance"></a>Helloblockchain-alkalmazás hozzáadása a példányhoz

1. Válassza ki a Blockchain Data Manager példányt a példány listából.
1. Válassza a **Blockchain-alkalmazások** elemet.
1. Válassza a **Hozzáadás** lehetőséget.

    ![Blockchain-alkalmazás hozzáadása](./media/data-manager-cosmosdb/add-application.png)

    Adja meg a blockchain-alkalmazás nevét és az intelligens szerződési ABI és bytecode URL-eket.

    Beállítás | Leírás
    --------|------------
    Név | Adjon meg egy egyedi nevet a nyomon követett blockchain-alkalmazás számára.
    Egyezményes ABI | A szerződéses ABI-fájl URL-címének elérési útja. További információkért lásd: [egyezményes ABI és bytecode URL-cím létrehozása](#create-contract-abi-and-bytecode-url).
    Szerződés bytecode | A bytecode fájl URL-elérési útja. További információkért lásd: [egyezményes ABI és bytecode URL-cím létrehozása](#create-contract-abi-and-bytecode-url).

1. Válassza az **OK** lehetőséget.

    Az alkalmazás létrehozása után az alkalmazás megjelenik a blockchain-alkalmazások listájában.

    ![Blockchain-alkalmazások listája](./media/data-manager-cosmosdb/artifact-list.png)

Törölheti az Azure Storage-fiókot, vagy felhasználhatja további blockchain alkalmazások konfigurálásához. Ha törölni szeretné az Azure Storage-fiókot, törölheti az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó tárfiók, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

## <a name="create-azure-cosmos-db"></a>Create Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Adatbázis és tároló hozzáadása

Adatbázis és tároló létrehozásához használhatja a Azure Portal Adatkezelő is.

1. Válassza ki a **adatkezelő** elemet a Azure Cosmos db fiók lapjának bal oldali navigációs sávjában, majd válassza az **új tároló** elemet.
1. A **tároló hozzáadása** panelen adja meg az új tároló beállításait.

    ![Tároló beállításainak megadása](./media/data-manager-cosmosdb/add-container.png)

    | Beállítás | Leírás
    |---------|-------------|
    | Adatbázis-azonosító | Adja meg a **blockchain** nevet az új adatbázis neveként. |
    | Teljesítmény | Az átviteli sebesség **400** adategység/másodperc (ru/s) esetén. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.|
    | Tároló azonosítója | Adja meg az **üzeneteket** az új tároló neveként. |
    | Partíciókulcs | A **/MessageType** használata partíciós kulcsként. |

1. Válassza az **OK** lehetőséget. A Adatkezelő megjeleníti az új adatbázist és a létrehozott tárolót.

## <a name="create-logic-app"></a>Logikai alkalmazás létrehozása

A Azure Logic Apps segítségével az üzleti folyamatok és munkafolyamatok ütemezhetők és automatizálható, ha a rendszerek és szolgáltatások integrálására van szükség. A logikai alkalmazással Event Grid csatlakozhat a Azure Cosmos DBhoz.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Integráció** > **Logikai alkalmazás** lehetőséget.
1. Adja meg a logikai alkalmazás létrehozási helyének részleteit. Ha elkészült, válassza a **Létrehozás** lehetőséget.

    A logikai alkalmazások létrehozásával kapcsolatos további információkért lásd: [automatizált munkafolyamatok létrehozása Azure Logic Appsokkal](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Miután az Azure üzembe helyezte az alkalmazást, válassza ki a logikai alkalmazás erőforrását.
1. A Logic Apps Designer **sablonok** területén válassza az **üres logikai alkalmazás** lehetőséget.

### <a name="add-event-grid-trigger"></a>Event Grid trigger hozzáadása

Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. A Logic Apps-motor az eseményindító minden elindulásakor létrehoz egy logikaialkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot. Azure Event Grid triggerrel küldheti el a blockchain Event Gridról Cosmos DBre.

1. A Logic Apps Designerben keresse meg és válassza ki a **Azure Event Grid** -összekötőt.
1. Az **Eseményindítók** lapon válassza ki az **erőforrás-esemény bekövetkezésekor** lehetőséget.
1. Hozzon létre egy API-kapcsolódást a Event Grid témakörhöz.

    ![Event Grid-eseményindító beállításai](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Beállítás | Leírás
    |---------|-------------|
    | Előfizetés | Válassza ki azt az előfizetést, amely tartalmazza a Event Grid témakört. |
    | Erőforrás típusa | Válassza a **Microsoft. EventGrid. témakörök** elemet. |
    | Erőforrás neve | Válassza ki annak a Event Grid témakörnek a nevét, ahol a Blockchain Data Manager tranzakciós adatokat küld. |

### <a name="add-cosmos-db-action"></a>Cosmos DB művelet hozzáadása

Hozzon létre egy műveletet egy dokumentum létrehozásához Cosmos DB minden tranzakcióhoz. A tranzakciós üzenet típusaként adja meg az üzenetek kategorizálásához használt partíciós kulcsot.

1. Válassza az **Új lépés** lehetőséget.
1. A **válasszon műveletet** területen keressen **Azure Cosmos db**.
1. Válassza **Azure Cosmos DB > műveletek > létrehozás vagy frissítés dokumentum létrehozása** lehetőséget.
1. Hozzon létre egy API-kapcsolódást a Cosmos DB-adatbázishoz.

    ![Cosmos DB kapcsolatbeállítások](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Beállítás | Leírás
    |---------|-------------|
    | Kapcsolat neve | Válassza ki azt az előfizetést, amely tartalmazza a Event Grid témakört. |
    | DocumentDB-fiók | Válassza ki a [Azure Cosmos db fiók létrehozása](#create-azure-cosmos-db) szakaszban létrehozott DocumentDB-fiókot. |

1. Adja meg a korábban létrehozott Azure Cosmos DB **adatbázis-azonosítóját** és a **gyűjtemény azonosítóját** az [adatbázis és tároló hozzáadása](#add-a-database-and-container) szakaszban.

1. Válassza ki a **dokumentum** beállítását. A *dinamikus tartalom hozzáadása* felugró ablakban válassza a **kifejezés** lehetőséget, és másolja ki és illessze be a következő kifejezést:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    A kifejezés beolvassa az üzenet adat részét, és az azonosítót egy időbélyeg-értékre állítja be.

1. Válassza az **új paraméter hozzáadása** lehetőséget, és válassza a **partíciós kulcs értéke** lehetőséget.
1. Állítsa a **Partition Key értéket** a következőre: `"@{triggerBody()['data']['MessageType']}"` . Az értéket idézőjelek között kell megadni.

    ![Logic Apps Designer Cosmos DB beállításokkal](./media/data-manager-cosmosdb/create-action.png)

    Az érték beállítja a partíció kulcsát a tranzakciós üzenet típusára.

1. Kattintson a **Mentés** gombra.

A logikai alkalmazás figyeli a Event Grid témakört. Amikor új tranzakciós üzenetet küld a Blockchain Data Manager, a logikai alkalmazás létrehoz egy dokumentumot a Cosmos DBban.

## <a name="send-a-transaction"></a>Tranzakció küldése

Ezután küldje el a tranzakciót a blockchain-főkönyvbe a létrehozott elemek teszteléséhez. Használja az előfeltételként szolgáló oktatóanyagban létrehozott **HelloBlockchain** -szerződés **SendRequest hívás** -függvényét [: intelligens szerződések létrehozásához, összeállításához és üzembe helyezéséhez használja a Visual Studio Code](send-transaction.md)-ot.

1. A **SendRequest hívás** függvény meghívásához használja az Azure Blockchain Development Kit intelligens szerződésének interakciós lapját. Kattintson a jobb gombbal a **HelloBlockchain. Sol** elemre, majd válassza az **intelligens szerződési interakció megjelenítése lapot** a menüből.

    ![Válassza az intelligens szerződési interakció megjelenítése lapot a menüből](./media/data-manager-cosmosdb/contract-interaction.png)

1. Válassza a **SendRequest hívás** -szerződés művelet lehetőséget, és adja meg **a Hello, Blockchain!** a **requestMessage** paraméterhez. Válassza a **végrehajtás** lehetőséget a **SendRequest hívás** függvény tranzakción keresztüli meghívásához.

    ![SendRequest hívás művelet végrehajtása](./media/data-manager-cosmosdb/sendrequest-action.png)

A SendRequest hívás függvény a **RequestMessage** és az **állapot** mezőket állítja be. A **RequestMessage** aktuális állapota a **Hello, Blockchain** argumentum. Az **állapot** mező értéke továbbra is **kérés**.

## <a name="view-transaction-data"></a>Tranzakciós adatgyűjtés megtekintése

Most, hogy csatlakoztatta a Blockchain-Data Managert a Azure Cosmos DBhoz, megtekintheti a Blockchain tranzakciós üzeneteit Cosmos DB Adatkezelő.

1. Lépjen a Cosmos DB Adatkezelő nézetre. Például: **cosmosdb-blockchain > Adatkezelő > blockchain-adatok > üzenetek > elemek**.

    ![Cosmos DB Adatkezelő](./media/data-manager-cosmosdb/data-explorer.png)

    Adatkezelő listázza a Cosmos DB adatbázisban létrehozott blockchain-adatüzeneteket.

1. Tallózással keresse meg az üzeneteket az elemek AZONOSÍTÓjának kiválasztásával, és keresse meg a megfelelő tranzakciós kivonattal rendelkező üzenetet.

    [![Képernyőfelvétel: a kijelölt elemek blockchain tranzakciójának részletei.](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    A nyers tranzakciós üzenet a tranzakció részleteit tartalmazza. A tulajdonság adatai azonban titkosítva vannak.

    Mivel a HelloBlockchain intelligens szerződést hozzáadta a Blockchain Data Manager példányhoz, a rendszer egy **ContractProperties** típusú üzenetet is küld, amely dekódolt tulajdonság-információkat tartalmaz.

1. A tranzakció **ContractProperties** -üzenetének megkeresése. A listán szereplő következő üzenetnek kell lennie.

    [![Blockchain tranzakció részletei](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    A **DecodedProperties** tömb tartalmazza a tranzakció tulajdonságait.

Gratulálunk! Sikeresen létrehozott egy tranzakciónapló-Blockchain Data Manager és Azure Cosmos DB használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az oktatóanyaghoz használt erőforrásokat és erőforráscsoportokat. Erőforráscsoport törlése:

1. A Azure Portalban navigáljon az **erőforráscsoporthoz** a bal oldali navigációs ablaktáblán, és válassza ki a törölni kívánt erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzéséhez írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

További információ a blockchain-főkönyvek integrálásáról.

> [!div class="nextstepaction"]
> [A Ethereum Blockchain-összekötő használata a Azure Logic Apps](ethereum-logic-app.md)
