---
title: Azure IoT hub létrehozása az erőforrás-szolgáltató REST API használatával | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és kezelhet IoT Hub programozott módon az erőforrás-szolgáltató C# REST API használatával.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 2f393701b97be76acab49a627a195b337018fa29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144431"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>IoT hub létrehozása az erőforrás-szolgáltató REST API (.NET) használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Az Azure IoT-hubok programozott módon történő létrehozásához és kezeléséhez használhatja a [IoT hub erőforrás-szolgáltatót REST API](/rest/api/iothub/iothubresource) . Ez az oktatóanyag bemutatja, hogyan használhatja a IoT Hub erőforrás-szolgáltatót REST API IoT hub C#-programból való létrehozásához.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag elvégzéséhez az alábbiakra lesz szüksége:

* A Visual Studióval.

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .

* [Azure PowerShell 1,0](/powershell/azure/install-Az-ps) vagy újabb.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>A Visual Studio-projekt előkészítése

1. A Visual Studióban hozzon létre egy Visual C# Windows klasszikus asztali projektet a **Console app (.NET-keretrendszer)** projekt sablon használatával. Nevezze el a projekt **CreateIoTHubREST**.

2. Megoldáskezelő kattintson a jobb gombbal a projektre, majd kattintson a **NuGet-csomagok kezelése** elemre.

3. A NuGet csomagkezelő területén tekintse meg az **előzetes kiadást**, majd a **Tallózás** oldalon keresse meg a **Microsoft. Azure. Management. erőforráskezelő**. Válassza ki a csomagot, kattintson a **telepítés** gombra, a **változások áttekintése** **elemre**, majd kattintson az **Elfogadom** gombra a licencek elfogadásához.

4. A NuGet csomagkezelő eszközben keressen rá a **Microsoft. IdentityModel. clients. ActiveDirectory** kifejezésre.  Kattintson a **telepítés** gombra, a **változások áttekintése** lapon kattintson **az OK** gombra, majd kattintson az **Elfogadom** gombra a licenc elfogadásához.

5. A program. cs esetében cserélje le a meglévő **using** utasításokat a következő kódra:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. A program. cs programban adja hozzá a következő statikus változókat a helyőrző értékeinek kiváltásához. Az oktatóanyag korábbi részében az **ApplicationId**, a **SubscriptionId**, a **TenantId** és a **Password** megjegyzését készítettük. Az **erőforráscsoport neve** az IoT hub létrehozásakor használt erőforráscsoport neve. Már meglévő vagy új erőforráscsoportot is használhat. **IoT hub neve** a létrehozott IoT hub neve, például **MyIoTHub**. Az IoT hub nevének globálisan egyedinek kell lennie. A **központi telepítés neve** a központi telepítés neve, például **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Az erőforrás-szolgáltató REST API használatával hozzon létre egy IoT hubot

A [IoT hub erőforrás-szolgáltató REST API](/rest/api/iothub/iothubresource) használatával hozzon létre egy IoT hubot az erőforráscsoporthoz. Az erőforrás-szolgáltató REST API segítségével módosíthatja a meglévő IoT hubot.

1. Adja hozzá a következő metódust a program. cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Adja hozzá a következő kódot a **CreateIoTHub** metódushoz. Ez a kód egy **HttpClient** objektumot hoz létre a következő fejlécekben található hitelesítési jogkivonattal:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Adja hozzá a következő kódot a **CreateIoTHub** metódushoz. Ez a kód a JSON-ábrázolás létrehozásához és létrehozásához szükséges IoT hubot ismerteti. Az IoT Hubt támogató helyszínek aktuális listájának megtekintéséhez tekintse meg az [Azure status](https://azure.microsoft.com/status/):

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Adja hozzá a következő kódot a **CreateIoTHub** metódushoz. Ez a kód elküldi a REST-kérést az Azure-nak. A kód ezután ellenőrzi a választ, és lekéri a telepítési feladat állapotának figyelésére használható URL-címet:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Adja hozzá a következő kódot a **CreateIoTHub** metódus végéhez. Ez a kód az előző lépésben lekért **asyncStatusUri** -címeket használja az üzembe helyezés befejeződésére való várakozáshoz:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Adja hozzá a következő kódot a **CreateIoTHub** metódus végéhez. Ez a kód lekéri a létrehozott IoT hub kulcsait, és kinyomtatja őket a konzolra:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Az alkalmazás befejezése és futtatása

Most már elvégezheti az alkalmazást úgy, hogy meghívja a **CreateIoTHub** metódust a létrehozása és futtatása előtt.

1. Adja hozzá a következő kódot a **Main** metódus végéhez:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Kattintson a **Létrehozás** , majd a **megoldás létrehozása** lehetőségre. Javítsa ki az esetleges hibákat.

3. Kattintson a **hibakeresés** elemre, majd **indítsa el a hibakeresést** az alkalmazás futtatásához. A központi telepítés futtatása több percet is igénybe vehet.

4. Annak ellenőrzéséhez, hogy az alkalmazás hozzáadta-e az új IoT hubot, látogasson el a [Azure Portalra](https://portal.azure.com/) , és tekintse meg az erőforrások listáját. Másik megoldásként használja a **Get-AzResource** PowerShell-parancsmagot.

> [!NOTE]
> Ez az alkalmazás egy S1 szabványú IoT Hub hoz létre, amelynek számlázása. Ha elkészült, törölheti az IoT hubot a [Azure Portalon](https://portal.azure.com/) keresztül, vagy a befejezéskor a **Remove-AzResource PowerShell-** parancsmag használatával.

## <a name="next-steps"></a>Következő lépések

Most, hogy üzembe helyezett egy IoT hubot az erőforrás-szolgáltató REST API használatával, érdemes megvizsgálnia a következőket:

* További információ a [IoT hub erőforrás-szolgáltató REST API](/rest/api/iothub/iothubresource)képességeiről.

* A Azure Resource Manager képességeinek megismeréséhez olvassa el [Azure Resource Manager áttekintést](../azure-resource-manager/management/overview.md) .

Ha többet szeretne megtudni a IoT Hub fejlesztéséről, tekintse meg a következő cikkeket:

* [A C SDK bemutatása](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)