---
title: Oktatóanyag – egyéni műveletek & erőforrásokhoz
description: Ez az oktatóanyag azt ismerteti, hogyan hozható létre Azure-beli felügyelt alkalmazás egy egyéni Azure-szolgáltatóval.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 50a65583c41dd94b174a33432afcf42b31e67df0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96437048"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Oktatóanyag: felügyelt alkalmazás létrehozása egyéni műveletekkel és erőforrásokkal

Ebben az oktatóanyagban saját felügyelt alkalmazást hoz létre egyéni műveletekkel és erőforrásokkal. A felügyelt alkalmazás egy egyéni műveletet fog tartalmazni az `Overview` oldalon, egy egyéni erőforrástípust, amely külön menüelemként jelenik meg az `Table of Content` egyéni erőforrás lapon, valamint egy egyéni környezeti művelet.

Ez az oktatóanyag a következő lépéseket tartalmazza:

> [!div class="checklist"]
> * Felhasználói felület definíciós fájljának szerzője felügyelt alkalmazás példányának létrehozásához
> * Telepítési sablon készítése az [Azure egyéni szolgáltatóval](../custom-providers/overview.md), az Azure Storage-fiókkal és az Azure-függvénnyel
> * Szerzői nézet definíciós összetevője egyéni műveletekkel és erőforrásokkal
> * Felügyelt alkalmazás definíciójának telepítése
> * Felügyelt alkalmazás példányának üzembe helyezése
> * Egyéni műveletek végrehajtása és egyéni erőforrások létrehozása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez ismernie kell a következőket:

* [Felügyelt alkalmazás definíciójának létrehozása és közzététele](publish-service-catalog-app.md).
* A [Service Catalog alkalmazás üzembe helyezése Azure Portal használatával](deploy-service-catalog-quickstart.md).
* [Azure Portal felhasználói felület létrehozása a felügyelt alkalmazáshoz](create-uidefinition-overview.md).
* [Megtekintheti a definíciós](concepts-view-definition.md) összetevők képességeit.
* Az [Azure egyéni szolgáltatói](../custom-providers/overview.md) képességei.

## <a name="user-interface-definition"></a>Felhasználói felület definíciója

Ebben az oktatóanyagban egy felügyelt alkalmazást hoz létre, és a felügyelt erőforráscsoport egyéni szolgáltatói példányt, Storage-fiókot és-függvényt fog tartalmazni. Az ebben a példában használt Azure-függvény olyan API-t valósít meg, amely kezeli az egyéni szolgáltatói műveleteket a műveletekhez és az erőforrásokhoz. Az Azure Storage-fiók alapszintű tárolóként használható az egyéni szolgáltatói erőforrások számára.

A felügyelt alkalmazás példányának létrehozásához szükséges felhasználói felület definíciója tartalmazza a `funcname` és a `storagename` bemeneti elemeket. A Storage-fiók nevének és a függvény nevének globálisan egyedinek kell lennie. Alapértelmezés szerint a Function Files a [minta Function csomagból](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip)lesz üzembe helyezve, de módosítható úgy, hogy hozzáad egy bemeneti elemet a csomag hivatkozásához a *createUiDefinition.json*:

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

és kimenet *createUiDefinition.json*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

A minta teljes *createUiDefinition.js* a következő [hivatkozáson található: felhasználói felület elemei összetevők](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Sablon egyéni szolgáltatóval

Az egyéni szolgáltatóval felügyelt alkalmazási példány létrehozásához meg kell határoznia a **nyilvános** nevű egyéni szolgáltatói erőforrást, és be kell írnia a **Microsoft. CustomProviders/resourceProviders** nevet a **mainTemplate.js**. Ebben az erőforrásban meg kell határoznia a szolgáltatáshoz tartozó erőforrás-típusokat és műveleteket. Az Azure Function és az Azure Storage-fiók példányainak üzembe helyezéséhez adja meg a Type és a típusú erőforrásokat `Microsoft.Web/sites` `Microsoft.Storage/storageAccounts` .

Ebben az oktatóanyagban létre fog hozni egy `users` erőforrás-típust, egy `ping` egyéni műveletet és egy egyéni `users/contextAction` műveletet, amelyet egy egyéni erőforrás kontextusában fog végrehajtani `users` . Minden erőforrástípus és művelet esetében adjon meg egy végpontot, amely a [createUiDefinition.js](#user-interface-definition)által megadott nevű függvényre mutat. A **routingType** megadása az `Proxy,Cache` erőforrástípusok és `Proxy` a műveletek esetében:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

A minta teljes *mainTemplate.js* a következő [hivatkozáson található: telepítési sablon](reference-main-template-artifact.md)összetevő.

## <a name="view-definition-artifact"></a>Meghatározás megtekintése összetevő

A felügyelt alkalmazásban egyéni műveleteket és egyéni erőforrásokat tartalmazó felhasználói felület definiálásához **viewDefinition.jst** kell létrehoznia az összetevőn. A definíciós összetevők megtekintésével kapcsolatos további információkért lásd: [definíciós összetevő megtekintése Azure Managed Applicationsban](concepts-view-definition.md).

Ebben az oktatóanyagban az alábbiakat határozza meg:
* Az alapszintű szövegbevitelt használó egyéni műveletet jelképező eszköztár gombbal rendelkező *áttekintő* lap `TestAction` .
* Egy egyéni erőforrástípust képviselő *felhasználók* lap `users` .
* Egyéni erőforrás-művelet `users/contextAction` a *felhasználók* lapon, amelyet a rendszer a típusú egyéni erőforrás kontextusában hajt végre `users` .

Az alábbi példa egy "áttekintés" oldal konfigurációjának megtekintését szemlélteti:

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

Az alábbi példa a "felhasználók" erőforrások oldal konfigurációját tartalmazza egyéni erőforrás-művelettel:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

A minta teljes *viewDefinition.js* a következő [hivatkozáson található: definíciós összetevő megtekintése](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Felügyelt alkalmazás definíciója

Csomagolja a következő felügyelt alkalmazási összetevőket a zip Archive-be, majd töltse fel a Storage-ba:

* createUiDefinition.jsbekapcsolva
* mainTemplate.jsbekapcsolva
* viewDefinition.jsbekapcsolva

Minden fájlnak legfelső szintűnek kell lennie. Az összetevőkkel rendelkező csomag bármilyen tárolóban tárolható, például a GitHub blob vagy az Azure Storage-fiók blobja. Itt látható egy parancsfájl az alkalmazáscsomag Storage-fiókba való feltöltéséhez: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Futtassa az alábbi Azure CLI-szkriptet, vagy kövesse a Azure Portal a Service Catalog felügyelt alkalmazás-definíciójának üzembe helyezése című témakör lépéseit:

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájában írja be és válassza a **felügyelt alkalmazások központ** elemet.
2. A **felügyelt alkalmazások központban** válassza a **Service Catalog alkalmazás definíciója** elemet, majd kattintson a **Hozzáadás** gombra. 
    
    ![Szolgáltatás-katalógus hozzáadása](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Adja meg a Service Catalog-definíciók létrehozásához szükséges értékeket:

    * Adja meg a szolgáltatás-katalógus definíciójának egyedi **nevét** , a **megjelenítendő nevet** és a *leírást*(nem kötelező).
    * Válassza ki azt az **előfizetést**, **erőforráscsoportot** és **helyet** , ahová az alkalmazás-definíciót létre kívánja hozni. Használhatja ugyanazt az erőforráscsoportot, amelyet a zip-csomaghoz használ, vagy létrehozhat egy új erőforráscsoportot.
    * A **Package file URI** esetében adja meg az előző lépésben létrehozott zip-fájl elérési útját.

    ![Értékek megadása](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. A hitelesítési és a zárolási szint szakaszban válassza az **Engedélyezés hozzáadása** lehetőséget.

    ![Engedélyezés hozzáadása](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Válasszon ki egy Azure Active Directory csoportot az erőforrások kezeléséhez, majd kattintson **az OK gombra**.

   ![Engedélyezési csoport hozzáadása](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Ha megadta az összes értéket, válassza a **Létrehozás** lehetőséget.

   ![Felügyelt alkalmazás definíciójának létrehozása](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Felügyelt alkalmazás példánya

A felügyelt alkalmazás definíciójának telepítésekor futtassa az alábbi parancsfájlt, vagy kövesse a Azure Portal a felügyelt alkalmazás-példány egyéni szolgáltatóval történő üzembe helyezéséhez szükséges lépéseket:

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájában írja be és válassza a **felügyelt alkalmazások központ** elemet.
2. A **felügyelt alkalmazások központban** válassza a **Service Catalog-alkalmazások** elemet, majd kattintson a **Hozzáadás** gombra. 

    ![Felügyelt alkalmazás hozzáadása](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. A **Service Catalog-alkalmazások** lapon írja be a Service Catalog-definíció megjelenítendő neve kifejezést a keresőmezőbe. Válassza ki az előző lépésben létrehozott definíciót, és kattintson a **Létrehozás** gombra.

    ![Szolgáltatáskatalógus kiválasztása](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Adja meg a felügyelt alkalmazás példányának a Service Catalog-definícióból való létrehozásához szükséges értékeket:

    * Válassza ki azt az **előfizetést**, **erőforráscsoportot** és **helyet** , ahová az alkalmazás-példányt létre kívánja hozni.
    * Adjon meg egy egyedi Azure-függvény nevét és az Azure Storage-fiók nevét.

    ![Alkalmazásbeállítások](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Az érvényesítés után kattintson **az OK** gombra a felügyelt alkalmazás példányának telepítéséhez. 
    
    ![Felügyelt alkalmazás telepítése](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Egyéni műveletek és erőforrások

A Service Catalog alkalmazás példányának telepítése után két új erőforráscsoport van. Az első erőforráscsoport `applicationGroup` a felügyelt alkalmazás egy példányát tartalmazza, a második erőforráscsoport `managedResourceGroup` tárolja a felügyelt alkalmazás erőforrásait, beleértve az **egyéni szolgáltatót** is.

![Alkalmazás-erőforráscsoportok](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

Megtekintheti a felügyelt alkalmazás példányát, és **egyéni műveleteket** hajthat végre az "áttekintés" lapon a **felhasználók** egyéni erőforrás létrehozása a "felhasználók" lapon, valamint egyéni **környezeti művelet** futtatása egyéni erőforráson.

* Nyissa meg az "áttekintés" lapot, és kattintson a "művelet pingelése" gombra:

![Egyéni művelet végrehajtása](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Nyissa meg a "felhasználók" lapot, és kattintson a "Hozzáadás" gombra. Adja meg a bemeneteket az erőforrások létrehozásához és az űrlap elküldéséhez:

![A képernyőfelvételen a felhasználók közül kiválasztott Hozzáadás gomb látható.](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Nyissa meg a "felhasználók" lapot, válassza ki a "felhasználók" erőforrást, és kattintson az "egyéni környezet művelet" lehetőségre:

![A képernyőképen a kiválasztott egyéni környezet művelet látható.](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Segítség keresése

Ha kérdése van a Azure Managed Applicationsával kapcsolatban, próbálja meg megkérdezni a [stack overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Előfordulhat, hogy egy hasonló kérdést már megtettek és megválaszoltak, ezért először A feladás előtt érdemes megnézni. A címke hozzáadásával `azure-managedapps` gyors választ kaphat!

## <a name="next-steps"></a>Következő lépések

Ha közzétenné felügyelt alkalmazását az Azure Marketplace-en, tekintse meg az [Azure-beli felügyelt alkalmazások a Marketplace piactéren](../../marketplace/create-new-azure-apps-offer.md) című témakört.

További információ az [Egyéni Azure-szolgáltatókról](../custom-providers/overview.md).
