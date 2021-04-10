---
title: Media Services fiók ARM-sablon: Azure Media Services Leírás: Ez a cikk bemutatja, hogyan hozhat létre Media Services-fiókot ARM-sablon használatával.
szolgáltatások: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. Service: Media-Services MS. munkaterhelés: MS. topic: gyors üzembe helyezés MS. Date: 03/23/2021 MS. Author: inhenkel MS. Custom: subject-armqs

---

# <a name="quickstart-media-services-account-arm-template"></a>Rövid útmutató: Media Services Account ARM-sablon

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk bemutatja, hogyan használható egy Azure Resource Manager sablon (ARM-sablon) egy Media Services-fiók létrehozásához.

## <a name="introduction"></a>Bevezetés

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Az ARM-sablonokkal rendelkező olvasók továbbra is használhatják az [üzembe helyezés szakaszt](#deploy-the-template).

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-media-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ha korábban még soha nem telepített ARM-sablont, hasznos lehet az [Azure ARM-sablonok](../../azure-resource-manager/templates/index.yml) megismerése és az [oktatóanyag](../../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)áttekintése.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-media-services-create/) közül származik.

A JSON-kód kerítésének szintaxisa a következő:

:::code language="json" source="~/quickstart-templates/101-media-services-create/azuredeploy.json":::

Három Azure-erőforrástípus van definiálva a sablonban:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): Storage-fiók létrehozása
- [Microsoft. Media/Mediaservices](/azure/templates/microsoft.media/mediaservices): Media Services fiók létrehozása

## <a name="set-the-account"></a>A fiók beállítása

```azurecli-interactive

az account set --subscription {your subscription name or id}

```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

```azurecli-interactive

az group create --name {the name you want to give your resource group} --location "{pick a location}"

```

## <a name="assign-a-variable-to-your-deployment-file"></a>Változó társítása a telepítési fájlhoz

A kényelem érdekében hozzon létre egy változót, amely a sablonfájl elérési útját tárolja. Ez a változó megkönnyíti az üzembe helyezési parancsok futtatását, mert nem kell újra megadnia az elérési utat minden egyes üzembe helyezéskor.

```azurecli-interactive

templateFile="{provide the path to the template file}"

```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A rendszer kérni fogja a Media Services-fiók nevének megadását.

```azurecli-interactive

az deployment group create --name {the name you want to give to your deployment} --resource-group {the name of resource group you created} --template-file $templateFile

```

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Az alábbihoz hasonló JSON-válasznak kell megjelennie:

```json
{
  "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Resources/deployments/amsarmquickstartdeploy",
  "location": null,
  "name": "amsarmquickstartdeploy",
  "properties": {
    "correlationId": "{correlationid}",
    "debugSetting": null,
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
            "resourceGroup": "amsarmquickstartrg",
            "resourceName": "storagey44cfdmliwatk",
            "resourceType": "Microsoft.Storage/storageAccounts"
          }
        ],
        "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg",
        "resourceName": "{accountname}",
        "resourceType": "Microsoft.Media/mediaServices"
      }
    ],
    "duration": "PT1M10.8615001S",
    "error": null,
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg"
      },
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
        "resourceGroup": "amsarmquickstartrg"
      }
    ],
    "outputs": null,
    "parameters": {
      "mediaServiceName": {
        "type": "String",
        "value": "{accountname}"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.Media",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "mediaServices"
          }
        ]
      },
      {
        "id": null,
        "namespace": "Microsoft.Storage",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "storageAccounts"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "templateHash": "{templatehash}",
    "templateLink": null,
    "timestamp": "2020-11-24T23:25:52.598184+00:00",
    "validatedResources": null
  },
  "resourceGroup": "amsarmquickstartrg",
  "tags": null,
  "type": "Microsoft.Resources/deployments"
}

```

A Azure Portal ellenőrizze, hogy létrejöttek-e az erőforrásai.

![létrehozott gyors üzembe helyezési erőforrások](./media/quickstart-arm-template-account/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem tervezi az imént létrehozott erőforrások használatát, törölheti az erőforráscsoportot.

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az ARM-sablonok használatáról, kövesse a sablon létrehozása paraméterekkel, változókkal és egyéb műveletekkel című témakört.

> [!div class="nextstepaction"]
> [Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)