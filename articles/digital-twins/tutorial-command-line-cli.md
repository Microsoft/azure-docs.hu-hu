---
title: 'Oktatóanyag: gráf létrehozása az Azure Digital Twins-ban (CLI)'
titleSuffix: Azure Digital Twins
description: Oktatóanyag Azure digitális Twins-forgatókönyv létrehozásához az Azure CLI használatával
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: d155d0c4a18b254f66ff5fb58ea91dbee22d2c34
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496609"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>Oktatóanyag: Azure digitális Twins-gráf létrehozása az Azure CLI használatával

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

Ebben az oktatóanyagban egy gráfot fog létrehozni az Azure Digital Ikrekben modellek, ikrek és kapcsolatok használatával. A jelen oktatóanyag eszköze az Azure [ **CLI**-hez készült Azure Digital Twins-parancs](how-to-use-cli.md). 

A CLI-parancsok használatával alapvető Azure-beli digitális Twins-műveleteket hajthat végre, például modellek feltöltését, ikrek létrehozását és módosítását, valamint kapcsolatok létrehozását. A CLI-parancsok teljes készletének megjelenítéséhez tekintse meg az [ *az DT* Command Set dokumentációját](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest) is.

Ebben az oktatóanyagban a következő lesz:...
> [!div class="checklist"]
> * Környezet modellezése
> * Digitális ikerpéldányok létrehozása
> * Kapcsolatok hozzáadása gráfok létrehozásához
> * A gráf lekérdezése a kérdések megválaszolásához

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseinek elvégzéséhez először végre kell hajtania a következő előfeltételeket.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt **hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** .

### <a name="download-the-sample-models"></a>A minta modellek letöltése

Az oktatóanyag két előre megírt modellt használ, amelyek a C# teljes [körű minta projekt](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) részét képezik az Azure digitális Twins-hoz. A modell fájljai itt találhatók: 
* [*Room.jsbekapcsolva*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [*Floor.jsbekapcsolva*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

A fájlok a gépen való beszerzéséhez használja a fenti navigációs hivatkozásokat, és másolja a fájl törzseit a gépen lévő helyi fájlokba (*Room.jsbe* és *Floor.js*).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell munkamenet beállítása
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>Azure digitális Twins-példány előkészítése

Ha az Azure Digital Twins-t szeretné használni ebben a cikkben, először **be kell állítania egy Azure digitális Twins-példányt** és a használatához szükséges engedélyeket. Ha már rendelkezik egy Azure Digital Twins-példánnyal, amely az előző munkafolyamatból lett beállítva, akkor ezt a példányt használhatja.

Ellenkező esetben kövesse az útmutató [*: példány és hitelesítés beállítása*](how-to-set-up-instance-cli.md)című témakör utasításait. Az utasítások emellett azt is tartalmazzák, hogy az egyes lépések sikeresen befejeződtek, és készen állnak arra, hogy az új példány használatával belépjenek.

Miután beállította az Azure Digital Twins-példányt, jegyezze fel a következő értékeket, amelyekhez később csatlakoznia kell a példányhoz:
* a példány **_állomásneve_**
* a példány létrehozásához használt **Azure-előfizetés** . 

A példányhoz mindkét értéket elérheti a következő Azure CLI-parancs kimenetében: 

```azurecli-interactive
az dt show -n <ADT_instance_name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="Képernyőkép: Cloud Shell böngészőablak, amely az az DT show parancs kimenetét jeleníti meg. Ki van emelve az állomásnév mező és az előfizetés azonosítója (az azonosító mező része).":::

## <a name="model-a-physical-environment-with-dtdl"></a>Fizikai környezet modellezése a DTDL

Most, hogy a CLI és az Azure Digital Twins-példány be van állítva, megkezdheti a forgatókönyvek gráfjának összeállítását. 

Az Azure digitális Twins-megoldások létrehozásának első lépéseként a rendszer Twin [**modelleket**](concepts-models.md) határoz meg a környezet számára. 

A modellek hasonlók az objektumorientált programozási nyelvekben található osztályokhoz. felhasználói sablonokat biztosítanak a [digitális ikrek](concepts-twins-graph.md) számára a későbbi követéshez és létrehozáshoz. A rendszer a **digitális Twins Definition Language (DTDL)** nevű JSON-szerű nyelven íródott, és meghatározhatja a Twin *tulajdonságait*, *telemetria*, *kapcsolatait* és *összetevőit*.

> [!NOTE]
> A DTDL lehetővé teszi a digitális Twins- *parancsok* definícióját is. A parancsok azonban jelenleg nem támogatottak az Azure Digital Twins szolgáltatásban.

Navigáljon a gépre az [Előfeltételek](#prerequisites) szakaszban létrehozott fájl *Room.js* . Nyissa meg egy szerkesztőprogramban, és változtassa meg a következő módokon:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Modellek feltöltése az Azure Digital Twinsba

A modellek tervezése után fel kell töltenie őket az Azure Digital Twins-példányba. Ezzel konfigurálja az Azure Digital Twins szolgáltatás példányát a saját egyéni tartományának szókincsével. Miután feltöltötte a modelleket, létrehozhat kettős példányokat, amelyek használják azokat.

1. Ha Cloud Shell használatával szeretne modelleket hozzáadni, fel kell töltenie a modell fájljait Cloud Shell tárhelyére, hogy a fájlok elérhetők legyenek az azokat használó Cloud Shell parancs futtatásakor. Ehhez válassza a "fájlok feltöltése/letöltése" ikont, és válassza a "feltöltés" lehetőséget.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Képernyőkép: Cloud Shell böngészőablak, amely a feltöltés ikon kiválasztását mutatja.":::
    
    Navigáljon a számítógépen található fájl *Room.js* , és válassza a "Megnyitás" lehetőséget. Ezt követően ismételje meg ezt a lépést a *Floor.js*.

1. Ezután használja az az [**DT Model Create**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_create) parancsot az alább látható módon, hogy feltöltse a frissített *Room* modellt az Azure Digital Twins-példányba. A második parancs feltölt egy másik modellt, a *padlót*, amelyet a következő szakaszban is használhat a különböző típusú ikrek létrehozásához.

    ```azurecli-interactive
    az dt model create -n <ADT_instance_name> --models Room.json
    az dt model create -n <ADT_instance_name> --models Floor.json
    ```
    
    Az egyes parancsok kimenete a sikeresen feltöltött modellel kapcsolatos információkat jeleníti meg.

    >[!TIP]
    >A könyvtárban lévő összes modellt feltöltheti egy adott időpontban a `--from-directory` Model Create parancs beállításával is. További információ: [opcionális paraméterek az *az DT Model Create*](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_create-optional-parameters).

1. Ellenőrizze, hogy a modellek létre lettek-e hozva az az [**DT Model List**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_list) paranccsal az alábbi ábrán látható módon. Ezzel kinyomtatja az Azure Digital Twins-példányba feltöltött összes modell teljes körű információit. 

    ```azurecli-interactive
    az dt model list -n <ADT_instance_name> --definition
    ```
    
    Keresse meg a szerkesztett *Room* modellt az eredmények között:
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="Képernyőkép a Cloud Shell a Model LIST parancs eredményét mutatja, amely tartalmazza a frissített szobatípus-modellt." lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>Hibák

A CLI a szolgáltatás hibáit is kezeli. 

Futtassa újra a `az dt model create` parancsot, és próbálja meg újból feltölteni az imént feltöltött modellek egyikét, a második alkalommal:

```azurecli-interactive
az dt model create -n <ADT_instance_name> --models Room.json
```

Mivel a modellek nem írhatók felül, ezzel hibakódot ad vissza `ModelIdAlreadyExists` .

## <a name="create-digital-twins"></a>Digitális ikerpéldányok létrehozása

Most, hogy egyes modellek fel lettek töltve az Azure Digital Twins-példányba, létrehozhat [**digitális ikreket**](concepts-twins-graph.md) a modell definíciói alapján. A digitális ikrek az Ön üzleti környezetében lévő entitásokat képviselik – például az érzékelők egy farmon, egy épületben lévő helyiségek vagy egy autóban található fények. 

Digitális Twin létrehozásához használja az az [**DT Twin Create**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_create) parancsot. Arra a modellre kell hivatkoznia, amelyre a Twin alapul, és opcionálisan meghatározhatja a modell bármely tulajdonságának kezdeti értékeit is. Ebben a fázisban nem kell semmilyen kapcsolati információt átadnia.

1. Futtassa ezt a kódot a Cloud Shellban, hogy több ikreket hozzon létre, a korábban frissített *szobatípus* és egy másik modell, a *padló* alapján. Ne felejtse el, hogy a *helyiség* három tulajdonsággal rendelkezik, így a kezdeti értékekkel megadhat argumentumokat. (A tulajdonságértékek inicializálása általánosságban nem kötelező, de ehhez az oktatóanyaghoz szükség van.)

    ```azurecli-interactive
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":"80", "HumidityLevel":"60"}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > Ha Cloud Shellt használ a PowerShell-környezetben, előfordulhat, hogy el kell kerülnie az idézőjelek karaktereit ahhoz, hogy a `--properties` JSON-értéket helyesen lehessen értelmezni. Ezzel a szerkesztéssel a Room Twins létrehozásához szükséges parancsok így néznek ki:
    >
    > ```azurecli-interactive
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > Ez az alábbi képernyőképen jelenik meg.
    
    Az egyes parancsok kimenete megjeleníti a sikeresen létrehozott Twin-ket (beleértve a velük inicializált szobai ikrek tulajdonságait is).

1. Az alábbi ábrán látható módon ellenőrizheti, hogy az ikrek létre lettek-e hozva az az [**DT Twin Query**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_query) paranccsal. A lekérdezés megkeresi az összes digitális ikreket az Azure Digital Twins-példányban.
    
    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```
    
    Az eredmények között keresse meg a *room0*, a *room1*, a *floor0* és a *floor1* ikreket. Itt látható egy olyan részlet, amely a lekérdezés eredményének egy részét mutatja.
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Képernyőkép Cloud Shell a kettős lekérdezés részleges eredményét mutatja, beleértve a room0 és a room1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

### <a name="modify-a-digital-twin"></a>Digitális Twin módosítása

Módosíthatja a létrehozott Twin-példányok tulajdonságait is. 

1. Futtassa ezt [**az DT Twin Update**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_update) paranccsal, hogy megváltoztassa a *Room0* RoomName a *room0* és a *PresidentialSuite* között:

    ```azurecli-interactive
    az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > Ha Cloud Shellt használ a PowerShell-környezetben, előfordulhat, hogy el kell kerülnie az idézőjelek karaktereit ahhoz, hogy a `--json-patch` JSON-értéket helyesen lehessen értelmezni. Ezzel a szerkesztéssel a következő paranccsal frissítheti a Twin:
    >
    > ```azurecli-interactive
    > az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > Ez az alábbi képernyőképen jelenik meg.
    
    A parancs kimenete megjeleníti a Twin aktuális adatait, és az eredményben az új értéket kell látnia `RoomName` .

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="Képernyőkép Cloud Shell a frissítési parancs eredményéről, amely tartalmazza a PresidentialSuite RoomName." lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. A frissítés sikeres ellenőrzéséhez futtassa az az [**DT Twin show**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_show) parancsot a *room0* információinak megtekintéséhez:

    ```azurecli-interactive
    az dt twin show -n <ADT_instance_name> --twin-id room0
    ```
    
    A kimenetnek a frissített nevet kell tükröznie.

## <a name="create-a-graph-by-adding-relationships"></a>Gráf létrehozása kapcsolatok hozzáadásával

Ezután létrehozhat néhány **kapcsolatot** ezen ikrek között, hogy összekapcsolja őket egy [**kettős gráfban**](concepts-twins-graph.md). A kettős gráfok egy teljes környezet ábrázolására szolgálnak. 

Az egyik és a másik között létrehozható kapcsolatok típusai a korábban feltöltött [modellekben](#model-a-physical-environment-with-dtdl) vannak meghatározva. A [ *Floor* modell definíciója](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) azt adja meg, hogy a padlók egy nevű kapcsolati típussal rendelkezhetnek. Ez lehetővé teszi, hogy az egyes *emeleti* kapcsolatokból hozzon létre egy beépített típusú kapcsolatot a *benne található megfelelő* helyiségbe.

Kapcsolat hozzáadásához használja az az [**DT Twin kapcsolat létrehozása**](/cli/azure/ext/azure-iot/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_relationship_create) parancsot. Adja meg a kapcsolat létrejöttét, a kapcsolat típusát, valamint azt, hogy a kapcsolat milyen dupla kapcsolaton keresztül kapcsolódik. Végül adja meg a kapcsolatot egy egyedi AZONOSÍTÓval. Ha egy kapcsolat úgy van meghatározva, hogy tulajdonságok legyenek, a kapcsolat tulajdonságait is inicializálhatja ebben a parancsban.

1. A következő kód futtatásával vegyen fel egy, a korábban létrehozott *emeleti* ikrekből származó *tartalmaz* egy, a megfelelő *Twin.* A kapcsolatok neve *relationship0* és *relationship1*.

    ```azurecli-interactive
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >A [ *Floor* modellben](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) *található kapcsolat is* két tulajdonsággal lett definiálva, `ownershipUser` így a `ownershipDepartment` kapcsolatok létrehozásakor a kezdeti értékekkel is megadhat argumentumokat.
    > Ahhoz, hogy kapcsolatot hozzon létre ezekkel a tulajdonságokkal, adja hozzá a `--properties` fenti parancsok egyikét, a következőhöz hasonlóan:
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > Ha Cloud Shellt használ a PowerShell-környezetben, előfordulhat, hogy el kell kerülnie az idézőjelek karaktereit ahhoz, hogy a `--properties` JSON-értéket helyesen lehessen értelmezni.
    
    Az egyes parancsok kimenete a sikeresen létrehozott kapcsolattal kapcsolatos információkat jeleníti meg.

1. A következő parancsok bármelyikével ellenőrizheti a kapcsolatokat, amelyek az Azure Digital Twins-példányban lévő kapcsolatokat kérdezik le.
    * Az egyes emeletekről érkező kapcsolatok megtekintéséhez (az egyik oldal kapcsolatainak megtekintése):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor0
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor1
        ```
    * Az egyes helyiségekben érkező összes kapcsolat megtekintéséhez (a "másik" oldal kapcsolatának megtekintése):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id room0 --incoming
        az dt twin relationship list -n <ADT_instance_name> --twin-id room1 --incoming
        ```
    * Ha ezeket a kapcsolatokat egyenként szeretné keresni, azonosító szerint:
        ```azurecli-interactive
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor1 --relationship-id relationship1
        ```

Az ebben az oktatóanyagban beállított ikrek és kapcsolatok a következő koncepcionális diagramot alkotják:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Egy elméleti gráfot bemutató diagram. a floor0 a relationship0-on keresztül csatlakozik a room0-hez, és a floor1 a relationship1-on keresztül csatlakozik a room1-hoz." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>A Twin gráf lekérdezése a környezeti kérdések megválaszolásához

Az Azure Digital Twins egyik fő funkciója, hogy könnyen és hatékonyan [lekérdezheti](concepts-query-language.md) a Twin Graphot a környezettel kapcsolatos kérdések megválaszolásához. Az Azure CLI-ben ez az az [**DT Twin Query**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_query) paranccsal hajtható végre.

Futtassa az alábbi lekérdezéseket a Cloud Shellban, hogy válaszoljon a minta környezettel kapcsolatos kérdésekre.

1. **Mi a környezetem minden olyan entitása, amely az Azure Digital Twins-ban szerepel?** (az összes lekérdezése)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```

    Ez lehetővé teszi, hogy a környezetét egy pillantással átvegye, és győződjön meg róla, hogy minden úgy képviselteti magát, mintha az Azure digitális Ikreken belül lenne. Ennek eredménye az, hogy az egyes digitális Twin termékek a részleteket tartalmazzák. Íme egy részlet:

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Képernyőkép Cloud Shell a kettős lekérdezés részleges eredményét mutatja, beleértve a room0 és a room1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >Előfordulhat, hogy ez ugyanaz a parancs, amelyet korábban a [*digitális Twins létrehozása*](#create-digital-twins) szakaszban használt, hogy megkeresse az összes Azure digitális ikreket a példányban.

1. **Mik azok a szobák a saját környezetben?** (lekérdezés modell alapján)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    A lekérdezés bizonyos típusú ikrekre korlátozható, így pontosabb információkhoz juthat a megjelenített adatokról. Ennek eredménye a *room0* és a *room1*, de nem jeleníti meg a *floor0* vagy a *floor1* (mivel ezek padlók **, nem szobák** ).
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="Képernyőkép – Cloud Shell, amely a modell lekérdezésének eredményét mutatja, amely csak a room0 és a room1 tartalmazza." lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. **Mi a *floor0* összes Terme?** (lekérdezés kapcsolat alapján)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    A diagramon létesített kapcsolatok alapján lekérdezheti a lekérdezéseket, így információkat kaphat arról, hogy az ikrek hogyan vannak csatlakoztatva, vagy hogy a lekérdezés egy bizonyos területen legyen korlátozva. Csak *room0* van a *floor0*, így az egyetlen hely az eredményben.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="Képernyőkép a Cloud Shellről, amely a kapcsolati lekérdezés eredményét mutatja, beleértve a room0 is." lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > Figyelje meg, hogy a Twins azonosító (például a fenti lekérdezés *floor0* ) a metaadatok mező használatával kérdezi le `$dtId` . 
    >
    >Ha a Cloud Shell használatával futtat egy olyan metaadat-mezőket tartalmazó lekérdezést, amelyik az elsővel kezdődik `$` , akkor a kezdő kell elmenekülnie, `$` hogy Cloud Shell tudja, hogy nem változó, és a lekérdezési szövegben literálként kell használni. Ez a fenti képernyőképen jelenik meg.

1. **Mi a környezetem minden olyan kisvárosa, amely 75 fölötti hőmérséklettel rendelkezik?** (lekérdezés tulajdonság alapján)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    A diagramot a tulajdonságok alapján kérdezheti le, hogy különböző kérdésekre válaszoljon, beleértve a kiugró értékek megkeresését a környezetben, amelyeknek szüksége lehet a beavatkozásra. Más összehasonlító operátorok (,,, *<* *>* *=* vagy *! =*) is támogatottak. az eredmények itt jelennek meg, mert a *room1* 80-as hőmérséklettel rendelkezik.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="Képernyőkép – Cloud Shell, amely a tulajdonság lekérdezésének eredményét mutatja, amely csak room1 tartalmaz." lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. **Mi a *floor0* található összes szoba 75-nál magasabb hőmérséklettel?** (összetett lekérdezés)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    Azt is megteheti, hogy kombinálja a korábbi lekérdezéseket, mint például az SQL-ben, az olyan kombinációs operátorok használatával, mint a `AND` , `OR` `NOT` . A lekérdezés `AND` azt használja, hogy az előző lekérdezés a két hőmérséklet konkrétabb legyen. Az eredmény mostantól csak a 75-nál nagyobb hőmérsékletű szobákat tartalmazza, amelyek a *floor0* vannak – ami ebben az esetben egyikük sem. Az eredményhalmaz üres.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="Képernyőkép a Cloud Shell az összetett lekérdezés eredményét mutatja, amely nem tartalmaz elemeket." lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag elvégzése után kiválaszthatja, hogy mely erőforrásokat szeretné eltávolítani, attól függően, hogy mit szeretne tenni.

* **Ha azt tervezi, hogy folytatja a következő oktatóanyagot**, megtarthatja az itt beállított erőforrásokat, és újból felhasználhatja az Azure digitális Twins-példányát anélkül, hogy törölné a két elem között.

* **Ha továbbra is az Azure Digital Twins-példányt szeretné használni, de törli az összes modelljét, ikreket és kapcsolatát**, az az [**DT Twin kapcsolat törlése**](/cli/azure/ext/azure-iot/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_relationship_delete), az [**DT Twin delete**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_delete)és [**az DT Model delete**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_delete) parancsok használatával törölheti a példányban lévő kapcsolatokat, ikreket és modelleket.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Előfordulhat, hogy törölni szeretné a helyi számítógépen létrehozott modell-fájlokat is.

## <a name="next-steps"></a>Következő lépések 

Ebben az oktatóanyagban megkezdte az Azure Digital Twins használatát, ha létrehoz egy gráfot a példányban az Azure CLI használatával. Létrehozta a modelleket, a digitális ikreket és a kapcsolatokat, amelyek egy gráfot alkotnak. Néhány lekérdezést is futtatott a gráfon, hogy megtudja, milyen kérdésekre ad választ az Azure Digital Twins a környezetről.

Folytassa a következő oktatóanyaggal, amellyel kombinálhatja az Azure Digital Twins szolgáltatást más Azure-szolgáltatásokkal, és elvégezheti az adatvezérelt és végpontok közötti helyzetet:
> [!div class="nextstepaction"]
> [*Oktatóanyag: végpontok közötti megoldás összekötése*](tutorial-end-to-end.md)