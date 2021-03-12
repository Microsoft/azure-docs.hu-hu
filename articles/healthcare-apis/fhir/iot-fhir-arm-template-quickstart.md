---
title: 'Gyors útmutató: az Azure IoT-összekötő üzembe helyezése a FHIR (előzetes verzió) ARM-sablonnal'
description: Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe az Azure IoT Connectort a FHIR (előzetes verzió) egy Azure Resource Manager sablon (ARM-sablon) használatával.
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 01/21/2021
ms.openlocfilehash: b241484b8e8d981036fff4998d475b8b80fae42d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019343"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>Gyors útmutató: Azure Resource Manager (ARM) sablon használata az Azure IoT-összekötő üzembe helyezéséhez a FHIR (előzetes verzió)

Ebből a rövid útmutatóból megtudhatja, hogyan használhat egy Azure Resource Manager sablont (ARM-sablont) az Azure IoT-összekötő üzembe helyezéséhez a gyors egészségügyi együttműködési erőforrásokhoz (FHIR&#174;) *, az Azure API FHIR-szolgáltatásához. A FHIR készült Azure IoT Connector munkapéldányának üzembe helyezéséhez a sablon egy szülő Azure API-t is üzembe helyez a FHIR szolgáltatáshoz, valamint egy Azure IoT Central alkalmazást, amely a telemetria az Azure IoT-Összekötőbe exportálja FHIR. Az ARM-sablon végrehajtásával az Azure IoT Connector a Azure Portal, a PowerShell vagy a CLI használatával helyezhető üzembe a FHIR.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon ekkor megnyílik a Azure Portal a bejelentkezés után.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban Azure IoT-összekötő a FHIR ARM-sablonnal a Azure Portalban.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* Ha a kódot helyileg szeretné futtatni, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* Ha helyileg szeretné futtatni a kódot:
    * Egy bash-rendszerhéj (például git bash, amely a [git for Windows](https://gitforwindows.org)részét képezi).
    * [Azure CLI](/cli/azure/install-azure-cli)-vel.

---

## <a name="review-the-template"></a>A sablon áttekintése

A [sablon](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json) a következő Azure-erőforrásokat definiálja:

* **Microsoft. HealthcareApis/szolgáltatások**
* **Microsoft. HealthcareApis/szolgáltatások/iomtconnectors**
* **Microsoft. IoTCentral/IoTApps**

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

# <a name="portal"></a>[Portál](#tab/azure-portal)

A következő hivatkozásra kattintva telepítheti a FHIR készült Azure IoT-összekötőt a Azure Portal ARM sablonnal:

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban Azure IoT-összekötő a FHIR szolgáltatáshoz a Azure Portal ARM sablonjának használatával.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

Az **Azure API üzembe helyezése FHIR** oldalon:

1. Ha szeretné, módosítsa az **előfizetést** az alapértelmezettről egy másik előfizetésre.

2. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget, adja meg az új erőforráscsoport nevét, majd kattintson **az OK gombra**.

3. Ha létrehozott egy új erőforráscsoportot, válasszon ki egy **régiót** az erőforráscsoport számára.

4. Adja meg az új Azure API FHIR-példány nevét a **FHIR szolgáltatás nevében**.

5. Válassza ki a FHIR-hez készült Azure API **helyét** . A hely az erőforráscsoport régiójától eltérő lehet.

6. Adja meg a FHIR-példányhoz tartozó Azure IoT-összekötő nevét a **IoT-összekötő neve** alatt.

7. Adja meg az Azure IoT-összekötőn belül létrehozott kapcsolatok nevét a FHIR a **Csatlakozás nevében**. Ezt a kapcsolódást az Azure IoT Central alkalmazás használja a szimulált eszközök telemetria az Azure IoT-Összekötőbe FHIR való leküldéséhez.

8. Adja meg az új Azure IoT Central-alkalmazás nevét a **IoT középső névben**. Ez az alkalmazás *folyamatos beteg-figyelési* sablont használ egy eszköz szimulálása érdekében.

9. Válassza ki a IoT Central alkalmazás helyét **IoT Central hely** legördülő menüből. 

10. Válassza az **Áttekintés + létrehozás** lehetőséget.

11. Olvassa el a használati feltételeket, majd válassza a **Létrehozás** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Ha helyileg szeretné futtatni a PowerShell-parancsfájlokat, első lépésként adja meg az `Connect-AzAccount` Azure-beli hitelesítő adatait.

Ha az `Microsoft.HealthcareApis` erőforrás-szolgáltató még nincs regisztrálva az előfizetéséhez, a következő interaktív kóddal regisztrálhatja. A kód Azure Cloud Shellban való futtatásához válassza a **kipróbálás** a kód felső sarkában lehetőséget.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Ha az `Microsoft.IoTCentral` erőforrás-szolgáltató még nincs regisztrálva az előfizetéséhez, a következő interaktív kóddal regisztrálhatja. A kód Azure Cloud Shellban való futtatásához válassza a **kipróbálás** a kód felső sarkában lehetőséget.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

A következő kód használatával helyezheti üzembe az Azure IoT-összekötőt a FHIR szolgáltatáshoz az ARM sablonnal.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

Ha az `Microsoft.HealthcareApis` erőforrás-szolgáltató még nincs regisztrálva az előfizetéséhez, a következő interaktív kóddal regisztrálhatja. A kód Azure Cloud Shellban való futtatásához válassza a **kipróbálás** a kód felső sarkában lehetőséget.

```azurecli-interactive
az extension add --name healthcareapis
```

Ha az `Microsoft.IoTCentral` erőforrás-szolgáltató még nincs regisztrálva az előfizetéséhez, a következő interaktív kóddal regisztrálhatja.

```azurecli-interactive
az extension add --name azure-iot
```

A következő kód használatával helyezheti üzembe az Azure IoT-összekötőt a FHIR szolgáltatáshoz az ARM sablonnal.

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> A telepítés elvégzése néhány percet vesz igénybe. Jegyezze fel a FHIR szolgáltatáshoz, az Azure IoT Central alkalmazáshoz és az erőforráscsoporthoz tartozó Azure API nevét, amelyet később az üzembe helyezett erőforrások áttekintésére használ.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépéseket követve áttekintheti az új Azure API-t a FHIR szolgáltatáshoz:

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **Azure API**-t a FHIR.

2. A FHIR listából válassza ki az új szolgáltatást. Megjelenik az új Azure API for FHIR szolgáltatás **áttekintő** lapja.

3. Annak ellenőrzéséhez, hogy az új FHIR API-fiók ki van-e választva, kattintson a **FHIR metaadat-végpont** melletti HIVATKOZÁSRA a FHIR API-képesség utasításának beolvasásához. A hivatkozás formátuma: `https://<service-name>.azurehealthcareapis.com/metadata` . Ha a fiók van kiépítve, egy nagyméretű JSON-fájl jelenik meg.

4. Annak ellenőrzéséhez, hogy a FHIR új Azure IoT-összekötője van-e kiépítve, válassza ki a **IoT-összekötőt (előzetes verzió)** a bal oldali navigációs menüből a **IoT-összekötők** lap megnyitásához. Az oldalnak a kiépített Azure IoT-összekötőt kell megjelenítenie, amely az *állapot* értéke *online*, a *kapcsolatok* értéke *1*, az *eszköz-hozzárendelési* és a *FHIR-hozzárendelés* pedig a *siker* ikont jeleníti meg.

5. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki **IoT Central alkalmazásokat**.

6. Az IoT Central alkalmazások listájában válassza ki az új szolgáltatást. Megjelenik az új IoT Central alkalmazás **Áttekintés** lapja.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

A következő interaktív kód futtatásával tekintheti meg az Azure API FHIR-szolgáltatással kapcsolatos részleteit. Meg kell adnia az új FHIR szolgáltatás és az erőforráscsoport nevét.

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> A FHIR készült Azure IoT-összekötő jelenleg nem biztosít PowerShell-parancsokat. Ha ellenőrizni szeretné, hogy az Azure IoT-összekötő a FHIR megfelelően lett-e kiépítve, használja a **portál** lapon megadott ellenőrzési folyamatot.

A következő interaktív kód futtatásával tekintheti meg az Azure IoT Central alkalmazás részleteit. Meg kell adnia az új IoT Central alkalmazás és az erőforráscsoport nevét.

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

A következő interaktív kód futtatásával tekintheti meg az Azure API FHIR-szolgáltatással kapcsolatos részleteit. Meg kell adnia az új FHIR szolgáltatás és az erőforráscsoport nevét.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> A FHIR készült Azure IoT-összekötő jelenleg nem biztosít CLI-parancsokat. Ha ellenőrizni szeretné, hogy az Azure IoT-összekötő a FHIR megfelelően lett-e kiépítve, használja a **portál** lapon megadott ellenőrzési folyamatot.

A következő interaktív kód futtatásával tekintheti meg az Azure IoT Central alkalmazás részleteit. Meg kell adnia az új IoT Central alkalmazás és az erőforráscsoport nevét.

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Csatlakoztassa a IoT-adatait az Azure IoT-összekötővel a FHIR (előzetes verzió) szolgáltatáshoz
> [!IMPORTANT]
> Az útmutatóban megadott eszköz-hozzárendelési sablon úgy van kialakítva, hogy az adatexportálással (örökölt) működjön a IoT Centralon belül.

IoT Central alkalmazás jelenleg nem biztosít ARM-sablont, illetve PowerShell-és CLI-parancsokat az adatexportálás beállításához. Ezért kövesse az alábbi utasításokat a Azure Portal használatával.  

Miután telepítette a IoT Central alkalmazást, a két beépített szimulált eszköz elkezdi a telemetria generálását. Ebben az oktatóanyagban betöltjük a telemetria a *Smart vitals patch* Simulator-ből a FHIR-be a FHIR-hez készült Azure IoT-összekötőn keresztül. Ha a IoT-adatait az Azure IoT-Összekötőbe szeretné exportálni a FHIR-hez, [be kell állítania egy adatexportálást (örökölt) a IoT Centralon belül](../../iot-central/core/howto-export-data-legacy.md). Az adatexportálás (örökölt) lapon:
- Válassza az *Azure Event Hubs* exportálás célhelyként.
- Válassza a **Event Hubs névtér** mezőhöz tartozó *kapcsolatok karakterláncának használata* elemet.
- Adja meg az Azure IoT-összekötőt a FHIR azon csatlakozási karakterláncához, amely egy előző lépésben a **csatlakozási sztring** mezőhöz lett beszerzett.
- Tartsa *meg a* **telemetria** beállítást az **exportálandó** adatmezőhöz.

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>Eszközbeállítások megtekintése a FHIR készült Azure API-ban

Az Azure IoT Connector által létrehozott FHIR-alapú megfigyelési erőforrás (oka) t a Poster használatával tekintheti meg a FHIR-kiszolgáló FHIR. Állítsa be a [Poster-t, hogy hozzáférjen az Azure API-hoz a FHIR-hez](access-fhir-postman-tutorial.md) , és `GET` kérjen kérelmet a `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` megfigyelési FHIR erőforrásainak a pulzusszám értékkel való megtekintéséhez.

> [!TIP]
> Győződjön meg arról, hogy a felhasználó megfelelő hozzáféréssel rendelkezik az Azure API-hoz a FHIR adatsíkon. Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](configure-azure-rbac.md) használatával rendelje hozzá a szükséges adatsíkok-szerepköröket.

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **erőforráscsoportok** elemet.

2. Az erőforráscsoport listán válassza ki az erőforráscsoport nevét.

3. Az erőforráscsoport **Áttekintés** lapján válassza az **erőforráscsoport törlése** elemet.

4. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

Az ARM-sablonok létrehozásának folyamatát ismertető lépésenkénti oktatóanyagért lásd az [első ARM-sablon létrehozásával és üzembe helyezésével kapcsolatos oktatóanyagot](../../azure-resource-manager/templates/template-tutorial-create-first-template.md) .

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezte az Azure IoT Connectort a FHIR szolgáltatáshoz a FHIR-erőforráshoz készült Azure API-ban. Válasszon az alábbi lépések közül, ha többet szeretne megtudni a FHIR készült Azure IoT Connectorról:

Megismerheti az Azure IoT-összekötőn belüli adatáramlás különböző szakaszait az FHIR-ben.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő a FHIR-adatfolyamhoz](iot-data-flow.md)

Ismerje meg, hogyan konfigurálhatja az IoT-összekötőt eszköz-és FHIR-leképezési sablonok használatával.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő FHIR-leképezési sablonokhoz](iot-mapping-templates.md)

* A Azure Portal a FHIR készült Azure IoT Connector a IoT Connector (előzetes verzió) néven ismert. A FHIR a HL7 bejegyzett védjegye, és a HL7 engedélyével van használatban.
