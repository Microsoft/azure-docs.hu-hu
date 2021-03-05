---
title: Oktatóanyag – az Azure IoT Hub üzenetek gazdagítása
description: 'Oktatóanyag: az üzenetek dúsításának használata az Azure IoT Hub üzeneteihez'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 7ae85fa2559b8313bd35a761945bff946563e935
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199781"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Oktatóanyag: az Azure IoT Hub üzenet-gazdagítás használata

Az *üzenet-gazdagítás* azt ismerteti *, hogy az* Azure IoT hub milyen képességgel rendelkezik az üzenetek a kijelölt végpontba való küldése előtt további információkkal. Az üzenetek dúsításának használatának egyik oka az, hogy olyan adathalmazt tartalmazzon, amely az alsóbb rétegbeli feldolgozás egyszerűsítésére használható. Például az eszközök telemetria-üzeneteinek az eszközökhöz való bővítésével csökkentheti az ügyfelek terhelését, így az eszközökhöz tartozó Twin API-hívásokat is megteheti az adatokhoz. További információ: [az üzenet-gazdagítás áttekintése](iot-hub-message-enrichments-overview.md).

Ebben az oktatóanyagban két módszert láthat az IoT hub üzenet-dúsításának teszteléséhez szükséges erőforrások létrehozására és konfigurálására. Az erőforrások egy két tárolót tartalmazó Storage-fiókkal rendelkeznek. Az egyik tároló tárolja a dúsított üzeneteket, és egy másik tároló tárolja az eredeti üzeneteket. A csomagban szerepel egy IoT hub is, amely az üzeneteket fogadja, és azokat a megfelelő tárolóba irányítja, attól függően, hogy azok gazdagítva vannak-e vagy sem.

* Az első módszer az Azure CLI használata az erőforrások létrehozásához és az üzenet-útválasztás konfigurálásához. Ezután a [Azure Portal](https://portal.azure.com)használatával manuálisan definiálhatja a dúsításokat.

* A második módszer egy Azure Resource Manager-sablon használata az üzenetek útválasztásához és az üzenetek dúsításához szükséges erőforrások *és* konfigurációk létrehozásához.

Az üzenet-útválasztás és az üzenetek dúsításának konfigurálását követően az alkalmazás használatával üzeneteket küldhet az IoT hubhoz. A hub ezután mindkét tárolóeszközre irányítja őket. A rendszer csak a **dúsított** tároló számára a végpontnak küldött üzeneteket gazdagítja.

Az oktatóanyag elvégzéséhez az alábbi feladatokat végezheti el:

**IoT Hub üzenet-gazdagítás használata**
> [!div class="checklist"]
> * Első módszer: erőforrások létrehozása és az üzenetek útválasztásának konfigurálása az Azure CLI használatával. Az üzenet-gazdagítás manuális konfigurálása a [Azure Portal](https://portal.azure.com)használatával.
> * Második módszer: erőforrások létrehozása és az üzenetek útválasztásának és az üzenetek dúsításának konfigurálása Resource Manager-sablon használatával. 
> * Futtasson egy alkalmazást, amely egy IoT-eszközt szimulál, amely üzeneteket küld az elosztónak.
> * Tekintse meg az eredményeket, és ellenőrizze, hogy az üzenetek gazdagítása a várt módon működik-e.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell egy Azure-előfizetéssel. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- A [Visual Studio](https://www.visualstudio.com/) telepítése.

- Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. Az oktatóanyagban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>A IoT C# minták tárházának beolvasása

Töltse le a [IoT C#-mintákat](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) a githubról, és csomagolja ki őket. A tárházban számos alkalmazás, parancsfájl és Resource Manager-sablon található. Az oktatóanyaghoz használt eszközök a következők:

* A manuális módszerhez van egy CLI-szkript, amely az erőforrások létrehozásához használatos. Ez a szkript a/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli. azcli. Ez a szkript létrehozza az erőforrásokat, és konfigurálja az üzenet-útválasztást. A szkript futtatása után manuálisan hozza létre az üzenet-dúsításokat a [Azure Portal](https://portal.azure.com)használatával.
* Az automatizált metódushoz van egy Azure Resource Manager sablon. A sablon/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.js. Ez a sablon létrehozza az erőforrásokat, konfigurálja az üzenet-útválasztást, majd konfigurálja az üzenetek dúsítását.
* A használt harmadik alkalmazás az eszköz-szimulációs alkalmazás, amellyel üzeneteket küldhet az IoT hubhoz, és tesztelheti az üzenetek dúsítását.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Manuális beállítás és konfigurálás az Azure CLI használatával

A szükséges erőforrások létrehozása mellett az Azure CLI-szkript a két útvonalat is konfigurálja külön tárolók közötti végpontokra. Az üzenet-útválasztás konfigurálásával kapcsolatos további információkért tekintse meg az [útválasztással foglalkozó oktatóanyagot](tutorial-routing.md). Az erőforrások beállítása után a [Azure Portal](https://portal.azure.com) használatával konfigurálhatja az üzenetek dúsítását az egyes végpontokhoz. Ezután folytassa a tesztelési lépéssel.

> [!NOTE]
> Az összes üzenet mindkét végponthoz van irányítva, de csak a végpontra irányuló, konfigurált üzenet-dúsítású üzenetek lesznek bővítve.
>

Használhatja a következő parancsfájlt, vagy megnyithatja a parancsfájlt a letöltött adattár/Resources mappájából. A szkript a következő lépéseket hajtja végre:

* Hozzon létre egy IoT hubot.
* Tárfiók létrehozása.
* Hozzon létre két tárolót a Storage-fiókban. Egy tároló a dúsított üzenetekhez van, és egy másik tároló a nem dúsított üzenetekhez.
* Útválasztás beállítása két különböző Storage-fiókhoz:
    * Hozzon létre egy végpontot minden egyes Storage-fiók tárolóhoz.
    * Hozzon létre egy útvonalat a Storage-fiókok tárolójának összes végpontján.

Számos olyan erőforrás neve van, amelynek globálisan egyedinek kell lennie, például a IoT hub neve és a Storage-fiók neve. A szkript egyszerűbb futtatásához ezeket az erőforrásokat a *randomValue* nevű véletlenszerű alfanumerikus érték fűzi hozzá. A véletlenszerű érték egyszer jön létre a parancsfájl elején. A parancsfájlban szükség szerint az erőforrás-nevekhez van hozzáfűzve. Ha nem szeretné, hogy az érték véletlenszerű legyen, beállíthatja egy üres sztringre vagy egy adott értékre.

Ha még nem tette meg, nyisson meg egy Azure [Cloud Shell ablakot](https://shell.azure.com) , és győződjön meg arról, hogy a bash értékre van állítva. Nyissa meg a parancsfájlt a kibontott tárházban, válassza a CTRL + A billentyűkombinációt az összes kiválasztásához, majd válassza a CTRL + C billentyűkombinációt a másoláshoz. Azt is megteheti, hogy átmásolja a következő CLI-szkriptet, vagy megnyithatja közvetlenül Cloud Shellban. Illessze be a szkriptet a Cloud Shell ablakába. ehhez kattintson a jobb gombbal a parancssorra, és válassza a **Beillesztés** lehetőséget. A parancsfájl egyszerre egy utasítást futtat. Ha a parancsfájl futása leáll, válassza az **ENTER billentyűt** , és győződjön meg arról, hogy az utolsó parancsot futtatja. A következő kódrészlet a használt szkriptet mutatja be, a megjegyzéseket pedig elmagyarázza, hogy mit csinál.

Itt láthatók a szkript által létrehozott erőforrások. A *dúsított* érték azt jelenti, hogy az erőforrás a dúsítással rendelkező üzenetekhez használható. Az *eredeti* érték azt jelenti, hogy az erőforrás a nem dúsított üzenetekhez használható.

| Name | Érték |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| tároló neve | eredeti  |
| tároló neve | sokoldalú és  |
| IoT-eszköz neve | Contoso-test-Device |
| IoT Hub neve | ContosoTestHubMsgEn |
| Storage-fiók neve | contosostorage |
| végpont neve 1 | ContosoStorageEndpointOriginal |
| végpont neve 2 | ContosoStorageEndpointEnriched|
| útvonal neve 1 | ContosoStorageRouteOriginal |
| útvonal neve 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

Ezen a ponton az erőforrások mindegyike be van állítva, az üzenet-útválasztás pedig konfigurálva van. Megtekintheti az üzenet-útválasztási konfigurációt a portálon, és beállíthatja az üzenetek dúsítását a **dúsított** tárolóba kerülő üzenetekhez.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Az üzenet-gazdagítás manuális konfigurálása a Azure Portal használatával

1. Nyissa meg az IoT hubot az **erőforráscsoportok** kiválasztásával. Ezután válassza ki az oktatóanyaghoz beállított erőforráscsoportot (**ContosoResourcesMsgEn**). Keresse meg a IoT hubot a listából, és válassza ki. Válassza az IoT hub **üzenet-útválasztás** elemét.

   ![Üzenet-útválasztás kiválasztása](./media/tutorial-message-enrichments/select-iot-hub.png)

   Az üzenet-útválasztási ablaktáblán három lap található az **útvonalak**, az **Egyéni végpontok** és a **gazdagított üzenetek** használatával. Tallózással keresse meg az első két lapot, és tekintse meg a parancsfájl által beállított konfigurációt. Az üzenet-gazdagítás hozzáadásához használja a harmadik lapot. Gazdagabbá teheti az üzeneteket a végponthoz a **dúsított** Storage-tárolóhoz. Adja meg a nevet és az értéket, majd válassza ki a végpont **ContosoStorageEndpointEnriched** a legördülő listából. Az alábbi példa bemutatja, hogyan állíthat be egy olyan dúsítást, amely hozzáadja az IoT hub nevét az üzenethez:

   ![Első alkoholtartalom hozzáadása](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Adja hozzá ezeket az értékeket a ContosoStorageEndpointEnriched-végpont listájához.

   | Kulcs | Érték | Végpont (legördülő lista) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin. Tags. location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Vevőkód | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Ha az eszköz nem rendelkezik Twin-vel, az itt megadott érték karakterláncként lesz lepecsételve az üzenet-gazdagított értékhez. Az eszközhöz tartozó Twin-információk megtekintéséhez nyissa meg a hubot a portálon, és válassza az **IoT-eszközök** elemet. Válassza ki az eszközt, majd válassza a lap tetején található **eszközök Twin** lehetőséget.
   >
   > A Twin-adatok szerkesztésével hozzáadhat címkéket, például a helyet, és beállíthatja egy adott értékre. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](iot-hub-devguide-device-twins.md).

3. Ha elkészült, a panelnek a következő képhez hasonlóan kell kinéznie:

   ![Táblázat az összes hozzáadott alkoholtartalommal](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. A módosítások mentéséhez kattintson az **alkalmaz** gombra. Ugorjon a [tesztüzenet tesztelése](#test-message-enrichments) szakaszra.

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Létrehozás és konfigurálás Resource Manager-sablon használatával
A Resource Manager-sablonok segítségével létrehozhatja és konfigurálhatja az erőforrásokat, az üzenetek útválasztását és az üzenetek dúsítását.

1. Jelentkezzen be az Azure portálra. Válassza az **+ erőforrás létrehozása** lehetőséget a keresőmező létrehozásához. Adja meg a *sablon központi telepítését*, és keresse meg. Az eredmények ablaktábláján válassza a **template Deployment (üzembe helyezés egyéni sablon használatával)** lehetőséget.

   ![Template deployment a Azure Portal](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Válassza a **Létrehozás** lehetőséget a **template Deployment** ablaktáblán.

1. Az **Egyéni telepítés** panelen válassza a **saját sablon létrehozása lehetőséget a szerkesztőben**.

1. A **Sablon szerkesztése** panelen válassza a **fájl betöltése** lehetőséget. A Windows Intéző megjelenik. Keresse meg a fájl **template_messageenrichments.js** a **/IOT-hub/tutorials/Routing/SimulatedDevice/Resources**-ben a kibontott tárház kibontott fájljában. 

   ![Sablon kiválasztása a helyi gépről](./media/tutorial-message-enrichments/template-select.png)

1. Válassza a **Megnyitás** elemet a sablonfájl a helyi gépről való betöltéséhez. Betöltődik, és megjelenik a szerkesztési ablaktáblán.

   Ez a sablon úgy van beállítva, hogy a globálisan egyedi IoT hub-név és a Storage-fiók nevét használja egy véletlenszerű érték az alapértelmezett nevek végéhez való hozzáadásával, így a sablon módosítás nélkül is felhasználható.

   Itt láthatók a sablon betöltésével létrehozott erőforrások. A **dúsított** érték azt jelenti, hogy az erőforrás a dúsítással rendelkező üzenetekhez használható. Az **eredeti** érték azt jelenti, hogy az erőforrás a nem dúsított üzenetekhez használható. Ezek az Azure CLI-szkriptben használt értékek.

   | Name | Érték |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | tároló neve | eredeti  |
   | tároló neve | sokoldalú és  |
   | IoT-eszköz neve | Contoso-test-Device |
   | IoT Hub neve | ContosoTestHubMsgEn |
   | Storage-fiók neve | contosostorage |
   | végpont neve 1 | ContosoStorageEndpointOriginal |
   | végpont neve 2 | ContosoStorageEndpointEnriched|
   | útvonal neve 1 | ContosoStorageRouteOriginal |
   | útvonal neve 2 | ContosoStorageRouteEnriched |

1. Kattintson a **Mentés** gombra. Megjelenik az **Egyéni telepítés** panel, és megjeleníti a sablon által használt összes paramétert. Az egyetlen mező, amelyet meg kell adni az **erőforráscsoport** számára. Hozzon létre egy újat, vagy válasszon ki egyet a legördülő listából.

   Itt látható az **Egyéni telepítés** panel felső fele. Láthatja, hogy hol tölti ki az erőforráscsoportot.

   ![Egyéni üzembe helyezési ablaktábla felső fele](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Itt látható az **egyéni telepítési** ablaktábla alsó fele. Láthatja a többi paramétert és a használati feltételeket. 

   ![Az egyéni telepítési ablaktábla alsó fele](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Jelölje be a jelölőnégyzetet, és fogadja el a feltételeket és a kikötéseket. Ezután válassza a **vásárlás** lehetőséget a sablon telepítésének folytatásához.

1. Várjon, amíg a sablon teljes mértékben üzembe helyezhető. A folyamat ellenőrzéséhez válassza a harang ikont a képernyő tetején. Ha elkészült, folytassa a [tesztüzenet tesztelése](#test-message-enrichments) szakaszon.

## <a name="test-message-enrichments"></a>Üzenet-gazdagítás tesztelése

Az üzenetek dúsításának megtekintéséhez válassza az **erőforráscsoportok** lehetőséget. Ezután válassza ki az oktatóanyaghoz használni kívánt erőforráscsoportot. Válassza ki az IoT hubot az erőforrások listájából, és lépjen az **üzenetküldés** lehetőségre. Megjelenik az üzenet-útválasztási konfiguráció és a beállított bővítések.

Most, hogy az üzenet gazdagítása konfigurálva van a végponthoz, futtassa a szimulált eszköz alkalmazást, hogy üzeneteket küldjön az IoT hubhoz. A hub olyan beállításokkal lett beállítva, amelyek a következő feladatokat hajtják végre:

* A tárolási végpont ContosoStorageEndpointOriginal továbbított üzenetek nem lesznek gazdagítva, és a tárolóban lesznek tárolva `original` .

* A tárolási végpont ContosoStorageEndpointEnriched átirányított üzenetek a tárolóban lesznek gazdagítva és tárolva `enriched` .

A szimulált eszköz alkalmazás a kibontott letöltésben szereplő egyik alkalmazás. Az alkalmazás üzeneteket küld az [útválasztási oktatóanyag](tutorial-routing.md)egyes különböző üzenet-útválasztási módszereihez, beleértve az Azure Storage-t is.

Kattintson duplán a megoldás fájlra **IoT_SimulatedDevice. SLN** a kód megnyitásához a Visual Studióban, majd nyissa meg a **program.cs**. Helyettesítse be a jelölőhöz tartozó IoT hub nevét `{your hub name}` . Az IoT hub-állomásnév formátuma **{a hub neve}. Azure-Devices.net**. Ebben az oktatóanyagban a hub-gazdagép neve ContosoTestHubMsgEn.azure-devices.net. Ezután cserélje le a korábban mentett eszköz kulcsát, amikor futtatta a szkriptet a jelölőhöz tartozó erőforrások létrehozásához `{your device key}` .

Ha nem rendelkezik az eszköz kulcsával, lekérheti azt a portálról. A bejelentkezés után lépjen az **erőforráscsoportok** elemre, válassza ki az erőforráscsoportot, majd válassza ki az IoT hubot. Keresse meg a **IoT eszközeit** , és válassza ki az eszközt. A vágólapra másoláshoz kattintson az **elsődleges kulcs** melletti másolás ikonra.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Futtatás és tesztelés

Futtassa a konzol alkalmazást néhány percig. Az elküldött üzenetek az alkalmazás konzol képernyőjén jelennek meg.

Az alkalmazás másodpercenként elküld egy új üzenetet az eszközről a felhőbe az IoT Hubnak. Az üzenet egy JSON-szerializált objektumot tartalmaz az eszköz azonosítójával, a hőmérséklettel, a páratartalommal és az üzenet szintjével, amely alapértelmezés szerint `normal`. Véletlenszerűen rendeli hozzá a vagy a `critical` szintet `storage` , ami azt eredményezi, hogy az üzenet a Storage-fiókhoz vagy az alapértelmezett végponthoz lesz irányítva. A Storage-fiókban a **dúsított** tárolóba küldött üzenetek gazdagítva lesznek.

Több tárolási üzenet elküldése után megtekintheti az adatfájlokat.

1. Válassza az **Erőforráscsoportok** lehetőséget. Keresse meg az erőforráscsoportot, **ContosoResourcesMsgEn**, és jelölje ki.

2. Válassza ki a Storage-fiókját, amely a **contosostorage**. Ezután válassza a **Storage Explorer (előzetes verzió)** lehetőséget a bal oldali ablaktáblán.

   ![Storage Explorer kiválasztása](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Válassza a **blob-tárolók** lehetőséget a két használható tároló megjelenítéséhez.

   ![A Storage-fiókban található tárolók](./media/tutorial-message-enrichments/show-blob-containers.png)

A **dúsított** tárolóban lévő üzeneteknek az üzenetekben található alkoholtartalom-növeléssel kell rendelkezniük. Az **eredeti** tárolóban lévő üzenetekben a nyers üzenetek nem rendelkeznek alkoholtartalom-növeléssel. Bontsa ki az egyik tárolót, amíg el nem jut az aljára, és nyissa meg a legfrissebb üzenetfájl-fájlt. Ezután végezze el ugyanezt a másik tárolóban annak ellenőrzéséhez, hogy nincsenek-e a tárolóban lévő üzenetekhez hozzáadott bővítések.

Ha megtekinti a dúsított üzeneteket, a "saját IoT Hub" nevet kell látnia a hub nevével és a hellyel, valamint az ügyfél-AZONOSÍTÓval, a következőhöz hasonlóan:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Itt egy nem dúsított üzenet jelenik meg. Figyelje meg, hogy a "saját IoT Hub," devicelocation "és" Vevőkód "nem jelenik meg itt, mert ezeket a mezőket a dúsítások teszik elérhetővé. Ez a végpont nem rendelkezik alkoholtartalom-növeléssel.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagban létrehozott összes erőforrás eltávolításához törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben eltávolítja az IoT hubot, a Storage-fiókot és magát az erőforráscsoportot.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Erőforrások törlése az Azure CLI használatával

Az erőforráscsoport az [az group delete](/cli/azure/group#az-group-delete) paranccsal távolítható el. Visszahívás, amely az `$resourceGroup` oktatóanyag elején **ContosoResourcesMsgEn** értékre lett állítva.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következő lépések végrehajtásával konfigurálta és tesztelte az üzenet-gazdagítás hozzáadását IoT Hub üzenetekhez:

**IoT Hub üzenet-gazdagítás használata**

> [!div class="checklist"]
> * Első módszer: erőforrások létrehozása és az üzenetek útválasztásának konfigurálása az Azure CLI használatával. Az üzenet-gazdagítás manuális konfigurálása a [Azure Portal](https://portal.azure.com)használatával.
> * Második módszer: erőforrások létrehozása és az üzenetek útválasztásának és az üzenetek dúsításának konfigurálása Azure Resource Manager sablon használatával.
> * Futtasson egy alkalmazást, amely egy IoT-eszközt szimulál, amely üzeneteket küld az elosztónak.
> * Tekintse meg az eredményeket, és ellenőrizze, hogy az üzenetek gazdagítása a várt módon működik-e.

Az üzenetek bővítésével kapcsolatos további információkért lásd: [az üzenetek dúsításának áttekintése](iot-hub-message-enrichments-overview.md).

Az üzenetek útválasztásával kapcsolatos további információkért tekintse meg a következő cikkeket:

> [!div class="nextstepaction"]
> [Eszközről a felhőbe irányuló üzenetek küldése különböző végpontokra IoT Hub üzenet-útválasztás használatával](iot-hub-devguide-messages-d2c.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: IoT Hub Útválasztás](tutorial-routing.md)