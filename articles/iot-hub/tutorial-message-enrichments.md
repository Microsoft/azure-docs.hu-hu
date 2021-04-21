---
title: Oktatóanyag – Üzenet Azure IoT Hub használata
description: Oktatóanyag, amely bemutatja, hogyan használhatók az üzenetek Azure IoT Hub üzenetekhez
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 0d6c90120d050b6896161f50332faf447c3ed67b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788852"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Oktatóanyag: Üzenet Azure IoT Hub használata

*Az üzenettitkítások* azt a  Azure IoT Hub, hogy az üzenetek további információkkal vannak-e megbélyegzve, mielőtt a kijelölt végpontra küldené azokat. Az üzenetek gazdagításának egyik oka az, hogy olyan adatokat is tartalmaz, amelyek felhasználhatók az lefelé irányuló feldolgozás egyszerűsítésére. Ha például egy ikereszközcímkével bővíti az eszköz telemetriai üzeneteit, azzal csökkentheti az ügyfelek terhelését, hogy ikereszköz API-hívásokat kezdeményeznek erre az információra. További információ: [Az üzenetkkel való bővítés áttekintése.](iot-hub-message-enrichments-overview.md)

Ebben az oktatóanyagban kétféleképpen hozhatja létre és konfigurálhatja az IoT Hub üzenetei gazdagításának tesztelésére szükséges erőforrásokat. Az erőforrások egy tárfiókot tartalmaznak két tárolóval. Az egyik tároló tárolja a bővített üzeneteket, egy másik pedig az eredeti üzeneteket. Emellett egy IoT Hubot is tartalmaz, amely fogadja az üzeneteket, és a megfelelő tárolóba irányítja őket attól függően, hogy azok bővítettek-e vagy sem.

* Az első módszer az erőforrások létrehozása és az üzenetek útválasztásának konfigurálása az Azure CLI használatával. Ezután manuálisan határozhatja meg a gazdagításokat a [Azure Portal.](https://portal.azure.com)

* A második módszer egy Azure Resource Manager az erőforrások és az  üzenetirányítás és az üzenet gazdagításai konfigurációjának létrehozásához.

Miután az üzenetek útválasztásának és az üzenetek gazdagításának konfigurációja befejeződött, egy alkalmazással üzeneteket küldhet az IoT Hubnak. A központ ezután mindkét tárolóba el lesz ásva. Csak a bővített tároló végpontjára küldött üzenetek vannak bővítve. 

Az oktatóanyag elvégzéséhez a következő feladatokat kell elvégeznie:

**Üzenet IoT Hub használata**
> [!div class="checklist"]
> * Első módszer: Erőforrások létrehozása és üzenetek útválasztásának konfigurálása az Azure CLI használatával. Konfigurálja manuálisan az üzenetket bővítő üzeneteket a [Azure Portal.](https://portal.azure.com)
> * Második módszer: Hozzon létre erőforrásokat, és konfigurálja az üzenetek útválasztását és az üzenetek bővítődését egy Resource Manager használatával. 
> * Olyan alkalmazás futtatása, amely egy IoT-eszközt szimulál, és üzeneteket küld a hubnak.
> * Tekintse meg az eredményeket, és ellenőrizze, hogy az üzenet gazdagításai a várt módon működnek-e.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell egy Azure-előfizetéssel. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- A [Visual Studio](https://www.visualstudio.com/) telepítése.

- Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. Az oktatóanyagban található eszközminta MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy egyes vállalati és oktatási hálózati környezetek blokkolják ezt a portot. További információ és a probléma megoldásának módjai: Csatlakozás IoT Hub [(MQTT) .](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Az IoT C#-mintatár lekérése

Töltse le [az IoT C#-mintákat a](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) GitHubról, és csomagolja ki őket. Ez az adattár számos alkalmazást, szkriptet és Resource Manager tartalmaz. Az oktatóanyaghoz az alábbiakat kell használni:

* A manuális metódushoz egy CLI-szkript is használható az erőforrások létrehozásához. Ez a szkript az /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli fájlban található. Ez a szkript létrehozza az erőforrásokat, és konfigurálja az üzenetek útválasztását. A szkript futtatása után manuálisan hozza létre az üzenet gazdagítását a [Azure Portal.](https://portal.azure.com)
* Az automatizált metódushoz egy sablon Azure Resource Manager is. A sablon az /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.jstalálható. Ez a sablon létrehozza az erőforrásokat, konfigurálja az üzenetek útválasztását, majd konfigurálja az üzenet gazdagítását.
* A harmadik használt alkalmazás az Eszközszimulációs alkalmazás, amellyel üzeneteket küldhet az IoT Hubnak, és tesztelni tudja az üzenet gazdagítását.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Manuális beállítás és konfigurálás az Azure CLI használatával

A szükséges erőforrások létrehozása mellett az Azure CLI-szkript a végpontok két útvonalát is konfigurálja, amelyek külön tárolók. További információ az üzenetek útválasztásának konfigurálásról: [Útválasztási oktatóanyag.](tutorial-routing.md) Az erőforrások beállítása után a Azure Portal [konfigurálhatja](https://portal.azure.com) az egyes végpontok üzenet gazdagítását. Ezután folytassa a tesztelési lépéssel.

> [!NOTE]
> A rendszer minden üzenetet mindkét végpontra irányít, de csak a konfigurált üzenettitkításokkal a végpontra küldött üzenetek lesznek bővítve.
>

Használhatja a következő szkriptet, vagy megnyithatja a szkriptet a letöltött adattár /resources mappájában. A szkript a következő lépéseket hajtja végre:

* IoT Hub létrehozása.
* Tárfiók létrehozása.
* Hozzon létre két tárolót a tárfiókban. Az egyik tároló a bővített üzenetekhez, a másik pedig a nem bővített üzenetekhez való.
* Állítsa be az útválasztást a két különböző tárfiókhoz:
    * Hozzon létre egy végpontot az egyes tárfióktárolókhoz.
    * Hozzon létre egy útvonalat a tárfiók egyes tárolóvégpontjaihoz.

Számos erőforrásnévnek globálisan egyedinek kell lennie, például az IoT Hub neve és a tárfiók neve. A szkript könnyebb futtatása érdekében a rendszer hozzáfűzi ezeket az erőforrásneveket egy randomValue nevű véletlenszerű alfanumerikus *értékkel.* A véletlenszerű érték egyszer jön létre a szkript tetején. A rendszer szükség szerint hozzáfűzi az erőforrásnevekhez a szkriptben. Ha nem szeretné, hogy az érték véletlenszerű legyen, beállíthatja üres sztringre vagy egy adott értékre.

Ha még nem tette meg, nyisson meg egy Azure [Cloud Shell](https://shell.azure.com) ablakot, és győződjön meg arról, hogy a Bash van beállítva. Nyissa meg a kicsomagolt adattárban a szkriptet, az összes kijelöléséhez nyomja le a Ctrl+A billentyűkombinációt, majd a Ctrl+C billentyűkombinációval másolja ki. Másik lehetőségként másolja ki a következő CLI-szkriptet, vagy nyissa meg közvetlenül a Cloud Shell. Illessze be a szkriptet a Cloud Shell a jobb gombbal a parancssorra, és válassza a Beillesztés **parancsot.** A szkript egyszerre egy utasítást futtat. Miután a szkript futása leállt, **az Enter billentyűt választva** győződjön meg arról, hogy az utolsó parancsot futtatja. A következő kódblokk a használt szkriptet mutatja be megjegyzésekkel együtt, amelyek elmagyarázzák, hogy mit csinál.

Itt vannak a szkript által létrehozott erőforrások. *A Bővített* azt jelenti, hogy az erőforrás a bővítő üzenetekkel kapcsolatos. *Az Eredeti* azt jelenti, hogy az erőforrás nem bővített üzenetekhez való.

| Name | Érték |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| tároló neve | Eredeti  |
| tároló neve | Dúsított  |
| IoT-eszköz neve | Contoso –Test-Device |
| IoT Hub neve | ContosoTestHubMsgEn |
| tárfiók neve | contosostorage |
| végpont neve 1 | ContosoStorageEndpointOriginal |
| végpont neve 2 | ContosoStorageEndpointEndented|
| route Name 1 | ContosoStorageRouteOriginal |
| route Name 2 | ContosoStorageRouteEndented |

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

Ezen a ponton az erőforrások mind be vannak állítva, és az üzenetek útválasztása konfigurálva van. A portálon megtekintheti az üzenet-útválasztási konfigurációt, és beállíthatja a bővített tárolóba küldött üzenetek üzenetei **gazdagítását.**

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Manuálisan konfigurálja az üzenetek gazdagítását a Azure Portal

1. Az IoT Hubhoz az Erőforráscsoportok lehetőség **kiválasztásával menek.** Ezután válassza ki az oktatóanyaghoz beállított erőforráscsoportot **(ContosoResourcesMsgEn**). Keresse meg az IoT Hubot a listában, és válassza ki. Az IoT **Hubhoz** válassza az Üzenet-útválasztás lehetőséget.

   ![Üzenet-útválasztás kiválasztása](./media/tutorial-message-enrichments/select-iot-hub.png)

   Az üzenet-útválasztási panel három lapból **áll: Útvonalak,** **Egyéni végpontok** és **Gazdagítás üzenetek.** Böngésszen az első két lapon, és tekintse meg a szkript által beállított konfigurációt. A harmadik lapon hozzáadhatja az üzenetek bővítődését. Bővítsünk ki üzeneteket a enriched nevű storage-tároló **végpontjára.** Adja meg a nevet és az értéket, majd válassza ki a **ContosoStorageEndpointEndpointEndented** végpontot a legördülő listából. Az alábbi példa bemutatja, hogyan állíthat be olyan bővítődet, amely hozzáadja az IoT Hub nevét az üzenethez:

   ![Az első bővítő hozzáadása](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Adja hozzá ezeket az értékeket a ContosoStorageEndpointEndpointEndented végpont listájához.

   | Kulcs | Érték | Végpont (legördülő lista) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEndpointEntained |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEndpointEntained |
   |Vevőkód | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEndpointEntained |

   > [!NOTE]
   > Ha az eszköznek nincs ikereszköze, az itt megadott érték sztringként lesz megbélyegzve az üzenet bővítőiben található értékhez. Az ikereszköz-információkért a portálon válassza az **IoT-eszközök lehetőséget.** Válassza ki az eszközt, majd **válassza** az ikereszköz lehetőséget az oldal tetején.
   >
   > Az ikerinformációk szerkesztésével címkéket adhat hozzá, például a helyet, és beállíthatja egy adott értékre. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](iot-hub-devguide-device-twins.md).

3. Ha elkészült, a panelnek az alábbi képhez hasonlóan kell kinéznie:

   ![Tábla az összes hozzáadott bővíteménysel](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. A **módosítások mentéshez** válassza az Alkalmaz lehetőséget. Ugorjon [a Test message enrichments (Üzenet gazdagításának tesztelése) szakaszra.](#test-message-enrichments)

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Létrehozás és konfigurálás sablon Resource Manager használatával
A sablonsablonokkal Resource Manager erőforrásokat, az üzenetek útválasztását és az üzenetek gazdagítását hozhatja létre és konfigurálhatja.

1. Jelentkezzen be az Azure Portalra. A **keresőmező létrehozásához válassza az +** Erőforrás létrehozása lehetőséget. Írja be *a sablon üzembe helyezését,* és keresse meg. Az eredmények ablaktábláján válassza a Template deployment **(üzembe helyezés egyéni sablon használatával)** lehetőséget.

   ![Template deployment a Azure Portal](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Válassza **a Létrehozás** lehetőséget **Template deployment** panelen.

1. Az Egyéni **üzembe helyezés panelen** válassza a Saját sablon létrehozása lehetőséget a **szerkesztőben.**

1. A Sablon **szerkesztése panelen** válassza a Fájl **betöltése lehetőséget.** Windows Explorer megjelenik. Keresse meg **template_messageenrichments.jsfájlt** a kicsomagolt adatfájlban az **/iot-hub/Tutorials/Routing/SimulatedDevice/resources fájlban.** 

   ![Sablon kiválasztása a helyi gépről](./media/tutorial-message-enrichments/template-select.png)

1. Válassza **a Megnyitás** lehetőséget a sablonfájl a helyi gépről való betöltéséhez. Betöltődik, és megjelenik a szerkesztési panelen.

   Ez a sablon úgy van beállítva, hogy globálisan egyedi IoT Hub- és tárfióknevet használjon egy véletlenszerű érték az alapértelmezett nevek végéhez való hozzáadásával, így a sablont módosítások nélkül használhatja.

   A sablon betöltésével létrehozott erőforrások a következőek. **A Bővített** azt jelenti, hogy az erőforrás a bővítő üzenetekkel kapcsolatos. **Az eredeti** azt jelenti, hogy az erőforrás nem bővített üzenetekhez való. Ezek ugyanazok az értékek, mint az Azure CLI-szkriptben.

   | Name | Érték |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | tároló neve | Eredeti  |
   | tároló neve | Dúsított  |
   | IoT-eszköz neve | Contoso –Test-Device |
   | IoT Hub neve | ContosoTestHubMsgEn |
   | tárfiók neve | contosostorage |
   | végpont neve 1 | ContosoStorageEndpointOriginal |
   | végpont neve 2 | ContosoStorageEndpointEndented|
   | route Name 1 | ContosoStorageRouteOriginal |
   | route Name 2 | ContosoStorageRouteEndented |

1. Kattintson a **Mentés** gombra. Megjelenik az Egyéni **üzembe** helyezés panel, és megjeleníti a sablon által használt összes paramétert. Az egyetlen mező, amit be kell állítania, az **erőforráscsoport.** Hozzon létre egy újat, vagy válasszon ki egyet a legördülő listából.

   Itt látható az Egyéni üzembe helyezés **panel felső** fele. Láthatja, hogy hol tölti ki az erőforráscsoportot.

   ![Az Egyéni üzembe helyezés panel felső fele](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Itt látható az Egyéni üzembe helyezés panel **alsó** fele. Láthatja a többi paramétert, valamint a használati feltételeket. 

   ![Az Egyéni üzembe helyezés panel alsó fele](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Jelölje be a jelölőnégyzetet, hogy elfogadja a használati feltételeket. Ezután válassza **a Vásárlás** lehetőséget a sablon üzembe helyezésének folytatásához.

1. Várja meg a sablon teljes üzembe helyezését. A folyamat ellenőrzéshez válassza a képernyő tetején található harang ikont. Ha elkészült, folytassa a Test [message enrichments (Üzenet gazdagításának tesztelése) szakaszra.](#test-message-enrichments)

## <a name="test-message-enrichments"></a>Üzenetek gazdagításának tesztelése

Az üzenet gazdagításának megtekintéséhez válassza az **Erőforráscsoportok lehetőséget.** Ezután válassza ki az oktatóanyaghoz használt erőforráscsoportot. Válassza ki az IoT Hubot az erőforrások listájából, majd válassza az **Üzenetkezelés lehetőséget.** Megjelenik az üzenet-útválasztási konfiguráció és a konfigurált gazdagítások.

Most, hogy konfigurálta az üzenetket a végponthoz, futtassa a Simulated Device alkalmazást, hogy üzeneteket küldjön az IoT Hubnak. A központ olyan beállításokkal lett beállítva, amelyek a következő feladatokat végrehajtásához végrehajtásához képesek:

* A ContosoStorageEndpointOriginal tárolási végpontra küldött üzenetek nem lesznek bővítve, és a tárolóban lesznek `original` tárolva.

* A ContosoStorageEndpointEndpointEndented tárolási végpontra küldött üzenetek ki lesznek bővítve, és a tárolóban lesznek `enriched` tárolva.

A Simulated Device alkalmazás a kicsomagolt letöltés egyik alkalmazása. Az alkalmazás üzeneteket küld az útválasztási oktatóanyagban található egyes üzenet-útválasztási módszerekhez, beleértve az Azure Storage-et is. [](tutorial-routing.md)

Kattintson duplán a **IoT_SimulatedDevice.sln** fájlra a kód megnyitásához a Visual Studio, majd nyissa meg a **Program.cs fájlt.** Helyettesítse be az IoT Hub nevét a `{your hub name}` jelölővel. Az IoT Hub-állomásnév formátuma **{a központ neve}.azure-devices.net.** Ebben az oktatóanyagban a központi gazdagép neve ContosoTestHubMsgEn.azure-devices.net. Ezután helyettesítse be a szkript futtatásakor mentett eszközkulcsot a jelölő erőforrásainak `{your device key}` létrehozásához.

Ha nem tudja az eszközkulcsot, lekérheti a portálról. Bejelentkezés után válassza az **Erőforráscsoportok** lehetőséget, válassza ki az erőforráscsoportot, majd válassza ki az IoT Hubot. Keresse meg a **teszteszközt az IoT-eszközök** alatt, és válassza ki az eszközt. Kattintson az Elsődleges kulcs melletti másolás **ikonra** a vágólapra másoláshoz.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Futtatás és tesztelés

Futtassa a konzolalkalmazást néhány percig. Az elküldött üzenetek az alkalmazás konzolképernyőjén jelennek meg.

Az alkalmazás másodpercenként elküld egy új üzenetet az eszközről a felhőbe az IoT Hubnak. Az üzenet egy JSON-szerializált objektumot tartalmaz az eszköz azonosítójával, a hőmérséklettel, a páratartalommal és az üzenet szintjével, amely alapértelmezés szerint `normal`. Véletlenszerűen rendeli hozzá a vagy a szintet, ami azt okozza, hogy az üzenet a tárfiókhoz vagy az `critical` `storage` alapértelmezett végponthoz lesz irányítva. A tárfiókban **a bővített** tárolóba küldött üzenetek ki lesznek bővítve.

Több tárolási üzenet küldése után tekintse meg az adatokat.

1. Válassza az **Erőforráscsoportok** lehetőséget. Keresse meg a **ContosoResourcesMsgEn** erőforráscsoportot, és jelölje ki.

2. Válassza ki a **contosostorage tárfiókot.** Ezután válassza **Storage Explorer (előzetes verzió)** lehetőséget a bal oldali panelen.

   ![Válassza a Storage Explorer](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Válassza **a BLOBTÁROLÓK** lehetőséget a két használható tároló kiválasztásához.

   ![A tárfiókban található tárolók](./media/tutorial-message-enrichments/show-blob-containers.png)

A **enriched** nevű tároló üzenetei tartalmazzák az üzenetekben az üzenet gazdagításokat. Az eredeti nevű tárolóban **található üzenetekben** a nyers üzenetek nincsenek bővítve. Ha le kell fúrni az egyik tárolóba, amíg el nem jut az aljára, és nyissa meg a legújabb üzenetfájlt. Ezután tegye meg ugyanezt a másik tárolóval annak ellenőrzéséhez, hogy nincsenek-e bővítve a tárolóban található üzenetek.

A bővített üzenetek megtekintésekor a "saját IoT Hub" üzenetnek kell látsza lennie a központ nevével, helyével és ügyfél-azonosítójával, a következőhöz hasonlóval:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Megjelenik egy nem küldött üzenet. Figyelje meg, hogy a "my IoT Hub", a "devicelocation" és a "customerID" nem adatokat tartalmaz, mert ezeket a mezőket a bővítők hozzáadják. Ez a végpont nem rendelkezik bővítődményekkel.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagban létrehozott összes erőforrás eltávolításához törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben eltávolítja az IoT Hubot, a tárfiókot és magát az erőforráscsoportot.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Erőforrások megtisztítása az Azure CLI használatával

Az erőforráscsoport az [az group delete](/cli/azure/group#az_group_delete) paranccsal távolítható el. Ne feledje, hogy az oktatóanyag elején `$resourceGroup` **a ContosoResourcesMsgEn** érték lett beállítva.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az alábbi lépésekkel konfigurálta és tesztelte az üzenetekhez IoT Hub üzenetkkel való bővítődését:

**Üzenet IoT Hub használata**

> [!div class="checklist"]
> * Első módszer: Erőforrások létrehozása és üzenetek útválasztásának konfigurálása az Azure CLI használatával. Konfigurálja manuálisan az üzenetket bővítő üzeneteket a [Azure Portal.](https://portal.azure.com)
> * Második módszer: Hozzon létre erőforrásokat, és konfigurálja az üzenetek útválasztását és az üzenetek bővítődését egy Azure Resource Manager használatával.
> * Olyan alkalmazás futtatása, amely egy IoT-eszközt szimulál, és üzeneteket küld a hubnak.
> * Tekintse meg az eredményeket, és ellenőrizze, hogy az üzenet gazdagításai a várt módon működnek-e.

Az üzenet gazdagítási folyamatokkal kapcsolatos további információkért lásd: [Az üzenetbe gazdagítások áttekintése.](iot-hub-message-enrichments-overview.md)

Az üzenet-útválasztásról az alábbi cikkekben talál további információt:

> [!div class="nextstepaction"]
> [Az IoT Hub üzenetek útválasztásának használatával az eszközről a felhőbe üzeneteket küldhet különböző végpontokra](iot-hub-devguide-messages-d2c.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: IoT Hub útválasztás](tutorial-routing.md)