---
title: Azure-IoT Hub létrehozása sablon (.NET) használatával | Microsoft Docs
description: Azure Resource Manager-sablon használata egy IoT Hub C# programmal való létrehozásához.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: db4b676e65d36a9476fd72b66cc8ccfa38af4d85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144502"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>IoT hub létrehozása Azure Resource Manager sablon használatával (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

A Azure Resource Manager használatával programozott módon hozhat létre és kezelhet Azure IoT-hubokat. Ebből az oktatóanyagból megtudhatja, hogyan használható egy Azure Resource Manager-sablon egy IoT hub C#-programból való létrehozásához.

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához:  [Azure Resource Manager és klasszikus](../azure-resource-manager/management/deployment-models.md).  Ez a cikk a Azure Resource Manager üzembe helyezési modell használatát ismerteti.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag elvégzéséhez az alábbiakra lesz szüksége:

* A Visual Studióval.
* Aktív Azure-fiók. <br/>Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial] .
* Egy [Azure Storage-fiók][lnk-storage-account] , amely a Azure Resource Manager sablonfájlokat tárolja.
* [Azure PowerShell 1,0][lnk-powershell-install] vagy újabb.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>A Visual Studio-projekt előkészítése

1. A Visual Studióban hozzon létre egy Visual C# Windows klasszikus asztali projektet a **Console app (.NET-keretrendszer)** projekt sablon használatával. Nevezze el a projekt **CreateIoTHub**.

2. Megoldáskezelő kattintson a jobb gombbal a projektre, majd kattintson a **NuGet-csomagok kezelése** elemre.

3. A NuGet csomagkezelő területén tekintse meg az **előzetes kiadást**, majd a **Tallózás** oldalon keresse meg a **Microsoft. Azure. Management. erőforráskezelő**. Válassza ki a csomagot, kattintson a **telepítés** gombra, a **változások áttekintése** **elemre**, majd kattintson az **Elfogadom** gombra a licencek elfogadásához.

4. A NuGet csomagkezelő eszközben keressen rá a **Microsoft. IdentityModel. clients. ActiveDirectory** kifejezésre.  Kattintson a **telepítés** gombra, a **változások áttekintése** lapon kattintson **az OK** gombra, majd kattintson az **Elfogadom** gombra a licenc elfogadásához.

5. A program. cs esetében cserélje le a meglévő **using** utasításokat a következő kódra:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. A program. cs programban adja hozzá a következő statikus változókat a helyőrző értékeinek kiváltásához. Az oktatóanyag korábbi részében az **ApplicationId**, a **SubscriptionId**, a **TenantId** és a **Password** megjegyzését készítettük. Az **Azure Storage-fiók neve** annak az Azure Storage-fióknak a neve, ahol a Azure Resource Manager sablonfájlokat tárolja. Az **erőforráscsoport neve** az IoT hub létrehozásakor használt erőforráscsoport neve. A név lehet egy már meglévő vagy egy új erőforráscsoport. A **központi telepítés neve** a központi telepítés neve, például **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Sablon elküldése egy IoT hub létrehozásához

A JSON-sablon és a paraméter fájl használatával hozzon létre egy IoT hubot az erőforráscsoporthoz. A meglévő IoT hub-t egy Azure Resource Manager sablonnal is módosíthatja.

1. Megoldáskezelőban kattintson a jobb gombbal a projektre, kattintson a **Hozzáadás**, majd az **új elem** lehetőségre. Vegyen fel egy **template.js** nevű JSON-fájlt a projektbe.

2. Ha standard IoT hub-t szeretne felvenni az **USA keleti** régiójába, cserélje le a **template.js** tartalmát a következő erőforrás-definícióval. Az IoT Hubt támogató régiók aktuális listájának megtekintéséhez tekintse meg az [Azure status][lnk-status]:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. Megoldáskezelőban kattintson a jobb gombbal a projektre, kattintson a **Hozzáadás**, majd az **új elem** lehetőségre. Vegyen fel egy **parameters.js** nevű JSON-fájlt a projektbe.

4. Cserélje le a **parameters.js** tartalmát a következő paraméter-információra, amely az új IoT hub nevét állítja be, például **{a Monogram} mynewiothub**. Az IoT hub nevének globálisan egyedinek kell lennie, ezért tartalmaznia kell a nevét vagy a monogramját:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. A **Server Explorerben** kapcsolódjon az Azure-előfizetéséhez, és az Azure Storage-fiókban hozzon létre egy **templates** nevű tárolót. A **Tulajdonságok** panelen állítsa a **sablonok** tárolóhoz tartozó **nyilvános olvasási hozzáférési** engedélyeket a **blob** elemre.

6. A **Server Explorerben** kattintson a jobb gombbal a **sablonok** tárolóra, majd kattintson a **blob-tároló megtekintése** elemre. Kattintson a **blob feltöltése** gombra, jelölje ki a két fájlt, **parameters.js** be és **templates.js** be, majd kattintson a **Megnyitás** gombra, és töltse fel a JSON-fájlokat a **sablonok** tárolóba. A JSON-fájlokat tartalmazó Blobok URL-címei a következők:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Adja hozzá a következő metódust a program. cs:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Adja hozzá a következő kódot a **CreateIoTHub** metódushoz a sablon és a paraméter fájljainak a Azure Resource Managerba való elküldéséhez:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Adja hozzá a következő kódot a **CreateIoTHub** metódushoz, amely megjeleníti az új IoT hub állapotát és kulcsait:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Az alkalmazás befejezése és futtatása

Most már elvégezheti az alkalmazást úgy, hogy meghívja a **CreateIoTHub** metódust a létrehozása és futtatása előtt.

1. Adja hozzá a következő kódot a **Main** metódus végéhez:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Kattintson a **Létrehozás** , majd a **megoldás létrehozása** lehetőségre. Javítsa ki az esetleges hibákat.

3. Kattintson a **hibakeresés** elemre, majd **indítsa el a hibakeresést** az alkalmazás futtatásához. A központi telepítés futtatása több percet is igénybe vehet.

4. Ha ellenőrizni szeretné, hogy az alkalmazás hozzáadta-e az új IoT hubot, látogasson el a [Azure Portalra][lnk-azure-portal] , és tekintse meg az erőforrások listáját. Másik megoldásként használja a **Get-AzResource** PowerShell-parancsmagot.

> [!NOTE]
> Ez az alkalmazás egy S1 szabványú IoT Hub hoz létre, amelynek számlázása. Az IoT hub törölhető a [Azure Portal][lnk-azure-portal] vagy a **Remove-AzResource PowerShell-** parancsmag használatával, ha elkészült.

## <a name="next-steps"></a>Következő lépések
Most üzembe helyezett egy IoT hub-t egy C#-programot használó Azure Resource Manager sablonnal, érdemes megvizsgálnia a következőket:

* További információ a [IoT hub erőforrás-szolgáltató REST API][lnk-rest-api]képességeiről.
* A Azure Resource Manager képességeinek megismeréséhez olvassa el [Azure Resource Manager áttekintést][lnk-azure-rm-overview] .
* A sablonokban használandó JSON-szintaxis és-tulajdonságok megtekintéséhez lásd: [Microsoft. Devices erőforrástípusok](/azure/templates/microsoft.devices/iothub-allversions).

Ha többet szeretne megtudni a IoT Hub fejlesztéséről, tekintse meg a következő cikkeket:

* [A C SDK bemutatása][lnk-c-sdk]
* [Azure IoT SDK-k][lnk-sdks]

A IoT Hub képességeinek további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md